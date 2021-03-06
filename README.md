[appscript]: http://appscript.sourceforge.net/

This is a CLI for OmniFocus. I had an AppleScript/Ruby monstrosity that actually worked with TaskPaper, The Hit List, Things and OmniFocus, but that one got out of hand. I took the good parts of it, concentrated on OmniFocus and converted it to [appscript][]. The result is OTask.

I don't know how long appscript is going to work for us, and it's now a dead project. I make no promises. This version of oTask has been tested on Ruby 2.0 (Mavericks) and with OmniFocus and OmniFocus 2.

### Installation

	$ [sudo] gem install otask

**Note:** If you use Xcode 5.1 and have trouble installing the gem, see [this post for a fix.](http://kaspermunck.github.io/2014/03/fixing-clang-error/)

### Documentation

OTask uses a custom syntax to allow entry of the various elements of an action in one line of text. The following formats can be used anywhere in the line, with the exception of the flag (!) which must be the last character on the line, preceded by a space.

 * @context			   (fragment, no spaces)
 * \#project             (fragment, no spaces)
 * due(due date)        (can be shortened as d(date))
 * create(creation date)    (can be shortened as c(date))
 * (notes)
 * !						(sets task as flagged)
 
Contexts and project specifiers should not include spaces. The algorithm that is used will find the best match for the string you give it, so you only need to include enough of it to distinguish it from other contexts or projects. For example, if I were going to put an action directly into my Markdown QuickTags folder, I could just use "#mdqt" and it will find it. "@corr" will get me the "correspondence" context.

Dates are entered in natural language format. You can type "tomorrow," "in 3 days," "next tuesday," etc. You can also use "+3" to set a date 3 days from the current day, "+7" for a week, and so on.

### Command line options

 -h, --help     Displays help message   
 -q, --quiet    Output as little as possible, overrides verbose   
 -V, --verbose  Verbose output   
 -g, --growl    Use Growl for feedback
 
### Example usage

	$ otask "Write a letter to mom"
	
This will put a task into your inbox with the name "Write a letter to mom." Nothing else will be set, it will wait there for you to pick it up.
 
	$ otask -g "Pick up the kids from school @err #single due(today 3pm) !"

This creates a new task in a project called Single Tasks, with a context of "errands", a due date of 3pm on the current day, and flags the task. 

The task will go to your inbox by default, and--if provided--project and context will be set. Your settings for automatic cleanup will determine what happens after that. Task elements not specified are left unset.

The `-g` parameter gives us our feedback via Growl, which is handy if you're calling it from a background script or application launcher like Quicksilver or LaunchBar.

	$ otask "Brainstorm for the morning meeting (Bill had some ideas, it might be worth checking in with him this afternoon) d(tomorrow 8am) #hipstartup @think"
	
This will create a task with a note. Everything in parenthesis is removed from the task name and placed into the notes of the action, sans parenthesis. Note that the due date prefix can be shortened to just "d".

OTask looks for notes in parenthesis, but it can also receive piped input from other applications as a note for the task. If you wanted to include text from a file, the output of a command or the plain-text contents of your clipboard, you can just pipe the output into the command, specifying the rest of the options as usual.

	$ pbpaste | otask "Notes from the morning meeting @ref"
	
That would take the current contents of your clipboard and make them the attached note on the "Notes from the morning meeting" task (with the context "reference").

### Calling from LaunchBar (et al.)

You can do this with any app that can run a script with input, or call it from automated scripts if you could think of a reason to. Below is the AppleScript for a LaunchBar action. Create a new script in AppleScript Editor and paste the code in. Edit the path in the last function to point to wherever you put the otask script. Save the AppleScript as OTask.scpt in `~/Library/Application Support/LaunchBar/Actions`. 

You'll find the Action in LaunchBar after it indexes. Type 'ota' (or as much as you need to get it to come up) and then press space bar. Use the syntax shown above to write out your action and its elements, but leave out the 'otask' part and any parameters. Hit return and Growl (you have it [installed, right?](http://growl.info)) will tell you what's up.

	on handle_string(actionString)
		if (length of actionString is not 0) then
			my runRubyScript(actionString)
		end if
		open location "x-launchbar:hide"
	end handle_string

	on runRubyScript(action)
		do shell script "/usr/bin/otask -g \"" & action & "\""
	end runRubyScript


### Author

Brett Terpstra

### Copyright

Copyright (c) 2011 Brett Terpstra. Licensed under the MIT License:

<http://www.opensource.org/licenses/mit-license.php>
