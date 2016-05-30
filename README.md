# ScriptOnlyUI Library

ScriptOnlyUI is a library that will automatically save and regenerate
objects and their properties on a scriptOnly LiveCode stack. The 
intention is to simplify the use of version control.

ScriptOnlyUI takes a minimalist approach and only supports a limited
subset of LiveCode features. Essentially anything that makes version
control of object properties complicated has been deliberately left out.

*   no image text so all images must use filenames
*   no custom properties so all custom properties must be set in script
*   no object ID
*   no ID means: 
    *   using behavior stacks or setting behaviors at runtime
    *   no icons so all icons must be set in script
*   no scripts so all object handlers must be handled in the script only
    script
*   all objects must have unique names and will be assigned uuid based 
    names if they don't have a name  
*   any session state (field text etc) in object properties must be 
    cleared during `saveStackRequest` to avoid merge conflicts    
*   no support for shared groups at this stage although it is probably
    possible
*   no support for substacks

A file with the same filename as the script only stack but with a
`*.livecodescriptui` extension is saved next to the stack.

In order to use the library open it in LiveCode and run the following
command:

    start using stack "ScriptOnlyUI"
    
Now create a script only stack and add the following handler to the
script:

    on ScriptOnlyUI
        return true
    end ScriptOnlyUI

From now on whenever you save the stack any controls will be saved
in the file alongside it in a format that is easy to read and edit in
a text editor.

At the moment there are a few issues that need resolving before this can
be used in any significant way:

*   Setting the visible of a stack to true during `preOpenStack` will
    trigger the window to open prematurely so at the moment it is left
    to the stack author to set the visible some time later
*   It is not currently possible to import a widget from an array into
    a group and relayering doesn't work unless the card is open
*   It only supports array properties that have a single dimension. This
    is OK for legacy control properties (at the moment) but unlikely to
    be OK for widget state properties.
