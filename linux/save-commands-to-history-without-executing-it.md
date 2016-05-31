# Save Commands To History Without Executing It

`history -s <YOUR-COMMAND>`

The above will save YOUR-COMMAND into the history of your bash without executing the command. You can press up arrow to access the command and execute it subsequently.

Use case?

In TMUX and Tmuxinator, when we start a project, we need to set commands such as `rails server` in each panel. However, we may not want to run the command (eg. `react-native run-android`).

Setting `history -s react-native run-android` allows us to save the command in the history and to easily run the command (by pressing up arrow and enter) when we need it.
