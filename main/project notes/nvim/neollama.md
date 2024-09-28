---
id: neollama
aliases: []
tags: []
---

# 07/05/24
Currently have window layouts for popup and input windows separately. Included appending of input value to chat popup window with User designation of input. Next step is to replicate interaction with Ollama using the API. This requires handling the streamed json response, in real time, and allowing for other API functionalities such as model switching. chat continuation, etc.

# 07/10/24
Redesigned everything. Finally have a captured response to append to the buffer. Next step is handling streamed responses. Would also like a toggleble settigns menu for different models, accessing saved chats, and other settings.

# 7/11/24
- Created a module to handle input windows which acts as a class ([[tables_as_classes_lua]]); possibly need to create a separate module for the popup or combine them into a window managing class. Next goal is to provide input to allow changing of API pereamaters through a command or key binding
- Updated model and prompt to be passed in dynamically. Made some window size adjustments to retain input window integrity when introducing toggelable settings window. Finally was able to capture streamed responses from API request, next step is updating the output functionality to append responses as they are recieved.

# 7/12/24
Having trouble using `nvim_buf_linecount` from within `on_stdout` callback since this is a loop function. Need to find a way of tracking and updating linecounts for the popup buffer without using this method. This also won't be possible until we can format the chunked response to respect line breaks and retain the original intended format.

# 7/17/24
Modularized code base; will need to be refactored for production. Completed feature to allow for visual selection of prompt from within a buffer. This feature does include a bug where the user must have entered visual mode at least once in the current neovim session before invoking the initialization function. Included keymaps to start the plugin and input keymaps to close out of windows synchrnously. Added a function to load model into memory when plugin is called making for faster responses. Need to create a menu to alter and interact with extra model parameters such as temperature, context window, etc. Included with this menu window should be options to change models or sessions depending on whether I choose to use conversation retention or the context value returned from the /generate endpoint to maintain conversations.

# 7/26/24
- Created function to parse paramaters from model details response. Need to implement way to apply defualt model paramaters before next call
- Fixed slow keymaps by binding the keymap to `{` rather than `[` for window navigation
- Need to work out bugs involving window navigation and tracking. I believe this can be fixed by creating a table update function for `window_selection` rather than on a as need be basis
- Need to find workaround for extended model names.I belive I can store the original name before it is shortened and access it from the index of the chosen item which should match that of the original name table.

# 8/23/24
Decided to start documenting again to better work through this bug. I've been working on reimplementing functions using the new layout system and one issue since then has been hiding the input while the api call is in process. Using the layout:update method to remove the input window is resulting in an err which is located in a function of the nui source. 

Upon investigation I've found that the function is responsible for redwaring the windows on update, show etc. in order to preserve the window size and relevance. The function iterates recursively from each component of the layout grabbing each of their borders window ids before calling a vim command which iterates over each of the widnows and foces a redraw, then moving the current window back to the original and this is where the error occurs. This is the function:
```lua
local function apply_workaround_for_float_relative_position_issue_18925(layout)
  local winids_len = 1
  local winids = { layout.winid }
  local function collect_anchor_winids(box)
    for _, child in ipairs(box.box) do
      if child.component then
        local border = child.component.border
        if border and border.winid then
          winids_len = winids_len + 1
          winids[winids_len] = border.winid
        end
      else
        collect_anchor_winids(child)
      end
    end
  end
  collect_anchor_winids(layout._.box)

  vim.schedule(function()
    -- check in case layout was immediately hidden or unmounted
    print(layout.winid .. ' ' .. winids[1])
    print(vim.inspect(winids))
    if layout.winid == winids[1] and vim.api.nvim_win_is_valid(winids[1]) then
      vim.cmd(
        ("noa call nvim_set_current_win(%s)\nnormal! jk\nredraw\n"):rep(winids_len):format(unpack(winids))
          .. ("noa call nvim_set_current_win(%s)"):format(vim.api.nvim_get_current_win())
      )
    end
  end)
end
```
(The added print statements are for debugging purposes).
Ex)

                                                             layout.winid will = table first element
                                                                                ^
  1004 1004 --> source prints from initial session open (layout.winid)          |
  { 1004, 1005, 1007, 1009, 1011 } --> source prints from initial session open  |
  hiding --> confirmation of hiding
  popup: 1015    -
  input: 1017    |
  session: 1021  | initial winids from neollama (before hiding)
  model: 1019    |
  layout: 1013   -
  1013 1013 --> source prints during hiding funciton
  { 1013, 1014, 1016, 1018, 1020 } --> source prints during hiding
  Error executing vim.schedule lua callback: vim/_editor.lua:0: nvim_exec2(): Vim(call):E5555: API call: Invalid window id: 1014
  stack traceback:
  	[C]: in function 'nvim_exec2'
  	vim/_editor.lua: in function 'cmd'
  	.../site/pack/packer/start/nui.nvim/lua/nui/layout/init.lua:47: in function <.../site/pack/packer/start/nui.nvim/lua/nui/layout/init.lua:42>
  1013 1013 --> ???
  { 1013, 1024, 1026, 1022 } --> ???
(The statements without labels are from the source)

It seems that when going through this loop the function is caught on one of the border ids and (seemingly because of the missing input window) provides an error stating the missing window. For now Ill see if I can rebuild the layout component rather than use the update method. This will require reloading the content of the buffer twice, once after the input is hidden and again after the response is provided and the input is replaced.

I was able to get the layout to update without bugs by using vim.schedule  with a 0 second timeout around the hide_input function. To be honest I'm not sure why this would work. My only guess is that somehow the immediate schedule allows for the switching of border windows from the source function to take place before any further action. Either way, it works lol.

# 8/28/24
Worked out the bugs involving switching models, sessions and configuring parameters. Need to now integrate a method pf handling the streamed response. I should also be thinking of how to refactor and restructure the code for production. Would also like to add a way to modify the system prompt, this will tie into the integration of a builtin web surfing tool.

For integrating the streamed response I think the best approach is as follows:
    - Initialize an empty response string
    - Process each response from within the on_stdout callback of the `ollamacall` function:
        - Capture the chunk
        - Use the last line in `nvim_buf_get_lines` to capture the current line
        - Run the current line with the chunk appended through `line_wrap` function to check if the line breaks are necessary

# 8/30/24
Need to rework how code blocks are handled from within the streamed response handling. It seems it doesn't respect newlines the same as other responses despite going through the initial handling as expected. My best bet would be to look at the values from within the for loop when this takes place. This is easier said then done since there's no debugger and my other best option is to print the values from within the loop which causes a massive flood of print messages.

# 9/3/24
- Reworked the streamed response handling by introducing a main function and a callback to separate the initial chunk
- Added a check to reset the cursor to the input window after showing the input
- Need to add an optional feature to have cursor follow streamed response as well as having the cursor auto load to the bottom of the conversation
- Also need a feature to resize the layout upon editor resizing to maintain useability
- Once these features are added the project can be refactored for production and the integration of the builtin web surfing agent and custom tools can be performed

# 9/27/24
Reformatted the entire drive on my kali install and lost a majority of my notes. Thankfully the necessary projects were hosted on github.

On another note the fresh drive allowed me to get a clean arch install which is pretty nice. I was able to test the plugin on a fresh install and workout some potential bugs but overall it works great. What's left is to work out the custom web agent and write out the docs. I'm not 100% on how to implement the web agent using lua as the only videos I've seen have used the python packages for Ollama or use hugging face to distribute the communication between models which is necessary for the agent creation. I could potentially create an external file which is executed through the plugin but i'm unsure of the implications of this method.

As for the web agent, I think the best option is to implement a python script to facilitate the RAG operations since it won't be fully possible from within the native tools. This option will be extremely slow so I'm gonna keep thinking of alternatives. I have a map for a general direction here: [[Ollama Web Agent.canvas|Ollama Web Agent]]

I could potentially restructure the scripts from the referenced videos into lua but i'm not sure how well that would work. I could potentially route the prompt first through an agent which decides whether it should search the web or not, then routing it to either the standard model or through the webscraper agent.