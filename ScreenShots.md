﻿#summary A few screenshots of the RevitPythonShell (RPS) plugin

# The RPS Add-Ins Ribbon #

The RPS Add-Ins Ribbon is added to the Add-Ins section of Revit when you install the plugin. It defaults to two commands:

  * Open Python Shell
  * Configure...

<img src='http://revitpythonshell.googlecode.com/svn/wiki/RPS_Ribbon.png' alt='Screenshot of the plugin ribbon' />

The command "Open Python Shell" displays the RPS interactive shell. The command "Configure..." displays the ConfigurationDialog.

For each CannedCommand configured in the ConfigurationDialog, an additional command is displayed in the ribbon. In the example screen shot, I have four such commands defined:

  * Save Snapshot
  * WireFrameViewer
  * SIA HTML Report
  * Animat Windows

These are only examples and **not** shipped with the plugin. You can configure your own CannedScript to help you get your work done!

# The RPS interactive shell #

The RPS interactive shell is displayed when you click on "Open Python Shell" in the RevitPythonShell Add-Ins Ribbon:

<img src='http://revitpythonshell.googlecode.com/svn/wiki/MainWindowScreenshot.Png' alt='Screenshot of the interactive shell' />

It consists of a menu with a default item "Configure Commands" that brings up the ConfigurationDialog. Also, it contains an entry for each CannedCommand you have configured. In the example screen shot, you see four such items, as explained above.

To get started with the interactive shell, you might want to look into the PredefinedVariables.

## AutoCompletionFeature ##

RPS includes an AutoCompletionFeature that is activated when you press `TAB`. This can be useful for exploring the Revit API.

<img src='http://revitpythonshell.googlecode.com/svn/wiki/RPS_AutoCompletionFeature.png' alt='Screenshot of the AutoCompletionFeature' />