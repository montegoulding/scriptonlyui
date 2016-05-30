script "ScriptOnlyUI"

constant kObjectSeparator = "------------------------------------------------------------------------"
constant kPropertySeparator = ";"
constant kPropertyNameSeparator = " : "
constant kPropertyValueKeySeparator = " - "
constant kPropertyValueElementSeparator = " -> "

on libraryStack
   if the target is me and there is not a button "ScriptOnlyUIFront" of me then
      set the defaultStack to me
      create button "ScriptOnlyUIFront"
      set the script of button "ScriptOnlyUIFront" to \
            "on preOpenStack" & return & \
            "    get the short name of the owner of the target" & return & \
            "    if it is not " & quote & "ScriptOnlyUI" & quote & " and \" & return & \
            "             the scriptOnly of stack it and \" & return & \
            "             the number of controls of stack it is 0 and \" & return & \
            "             the filename of stack it is not empty then" & return & \
            "        GenerateScriptOnlyUI it" & return & \
            "    end if" & return & \
            "    pass preOpenStack" & return & \
            "end preOpenStack" & return
      insert the script of btn "ScriptOnlyUIFront" of me into front
      
      create button "ScriptOnlyUIBack"
      set the script of button "ScriptOnlyUIBack" to \
            "on saveStackRequest" & return & \
            "    get the short name of the owner of the target" & return & \
            "    if it is not " & quote & "ScriptOnlyUI" & quote & " and the scriptOnly of stack it then" & return & \
            "        SaveScriptOnlyUI it" & return & \
            "    end if" & return & \
            "    pass saveStackRequest" & return & \
            "end saveStackRequest" & return
      insert the script of btn "ScriptOnlyUIBack" of me into back
   end if
   pass libraryStack 
end libraryStack

on releaseStack
   if the target is me then
      if there is a button "ScriptOnlyUIFront" then
         remove the script of btn "ScriptOnlyUIFront" from front
      end if
      if there is a button "ScriptOnlyUIBack" then
         remove the script of btn "ScriptOnlyUIBack" from front
      end if
   end if
   pass releaseStack
end releaseStack

private function EnsureScriptOnlyUI pStack
   dispatch "ScriptOnlyUI" to stack pStack
   return the result
end EnsureScriptOnlyUI

private function UIFile pStack
   local tFile
   put the filename of stack pStack into tFile
   set the itemDelimiter to "."
   put "livecodescriptui" into the last item of tFile
   return tFile
end UIFile

on GenerateScriptOnlyUI pStack
   if EnsureScriptOnlyUI(pStack) then
      set the defaultStack to pStack
      
      local tScreenRect
      put line (the screen of stack pStack) of the screenRects into tScreenRect
      set the location of stack pStack to item 1 of tScreenRect + (item 3 of tScreenRect - item 1 of tScreenRect) div 2, item 2 of tScreenRect + (item 4 of tScreenRect - item 2 of tScreenRect) div 2
      
      local tFile
      put UIFile(pStack) into tFile
      
      if there is a file tFile then
         local tObjects
         open file tFile for "utf-8" text read
         read from file tFile until "eof"
         put it into tObjects
         close file tFile
         
         --put textDecode(url ("binfile:" & tFile)) into tObjects
         split tObjects by (kObjectSeparator & return)
         
         local tUnlockUpdates
         
         repeat with tIndex = 1 to the number of elements of tObjects
            split tObjects[tIndex] by (kPropertySeparator & return) and kPropertyNameSeparator
            
            local tObject
            switch tObjects[tIndex]["type"]
               case "" -- stack
                  get the name of stack pStack
                  break
               case "card"
                  if the number of cards of stack pStack is 1 and \
                        the short name of card 1 of stack pStack is the abbreviated id of card 1 of stack pStack then
                     get the long id of card 1 of stack pStack
                  else
                     create card tObjects[tIndex]["name"]
                  end if
                  break
               case "group"
                  if word 1 of tObjects[tIndex]["owner"] is "group" then
                     create group tObjects[tIndex]["name"] in tObjects[tIndex]["owner"]
                  else
                     create group tObjects[tIndex]["name"]
                  end if
                  set the lockUpdates of it to true
                  put it into tUnlockUpdates[the number of elements of tUnlockUpdates + 1]
                  break
               case "button"
                  if word 1 of tObjects[tIndex]["owner"] is "group" then
                     create button tObjects[tIndex]["name"] in tObjects[tIndex]["owner"]
                  else
                     create button tObjects[tIndex]["name"]
                  end if
                  break
               case "field"
                  if word 1 of tObjects[tIndex]["owner"] is "group" then
                     create field tObjects[tIndex]["name"] in tObjects[tIndex]["owner"]
                  else
                     create field tObjects[tIndex]["name"]
                  end if
                  break
               case "graphic"
                  if word 1 of tObjects[tIndex]["owner"] is "group" then
                     create graphic tObjects[tIndex]["name"] in tObjects[tIndex]["owner"]
                  else
                     create graphic tObjects[tIndex]["name"]
                  end if
                  break
               case "image"
                  if word 1 of tObjects[tIndex]["owner"] is "group" then
                     create image tObjects[tIndex]["name"] in tObjects[tIndex]["owner"]
                  else
                     create image tObjects[tIndex]["name"]
                  end if
                  break
               case "player"
                  if word 1 of tObjects[tIndex]["owner"] is "group" then
                     create player tObjects[tIndex]["name"] in tObjects[tIndex]["owner"]
                  else
                     create player tObjects[tIndex]["name"]
                  end if
                  break
               case "scrollbar"
                  if word 1 of tObjects[tIndex]["owner"] is "group" then
                     create scrollbar tObjects[tIndex]["name"] in tObjects[tIndex]["owner"]
                  else
                     create scrollbar tObjects[tIndex]["name"]
                  end if
                  break
               case "widget"
                  CreateWidget tObjects[tIndex]
                  get the result
                  break
            end switch
            
            put it into tObject
            
            local tProperty
            repeat for each key tProperty in tObjects[tIndex]
               if tObjects[tIndex][tProperty] begins with return then
                  delete char 1 of tObjects[tIndex][tProperty]
               end if
               
               if tObjects[tIndex][tProperty] begins with kPropertyValueKeySeparator then
                  delete char 1 to the length of kPropertyValueKeySeparator of tObjects[tIndex][tProperty]
                  -- array property
                  split tObjects[tIndex][tProperty] by (return & kPropertyValueKeySeparator) and kPropertyValueElementSeparator
                  
                  repeat for each key tKey in tObjects[tIndex][tProperty]
                     if tObjects[tIndex][tProperty][tKey] begins with return then
                        delete char 1 of tObjects[tIndex][tProperty][tKey]
                     end if
                  end repeat
               end if
               
               set the tProperty of tObject to tObjects[tIndex][tProperty]
            end repeat
         end repeat
         
         repeat with tIndex = the number of elements of tUnlockUpdates down to 1
            set the lockUpdates of tUnlockUpdates[tIndex] to false
         end repeat
      end if
   end if
end GenerateScriptOnlyUI

on SaveScriptOnlyUI pStack
   if EnsureScriptOnlyUI(pStack) then
      ResetTemplateProperties
      
      local tFile
      put UIFile(pStack) into tFile
      open file tFile for "utf-8" text write
      if the result is empty then
         try
            SaveObject the long id of stack pStack, tFile
         catch tError
            answer error tError
         finally
            close file tFile
         end try
      end if
   end if
end SaveScriptOnlyUI

private command SaveObject pObject, pFile
   local tProperties, tType
   
   put word 1 of pObject into tType
   
   if tType is "widget" then
      put GetWidgetProperties(pObject) into tProperties
   else
      put the properties of pObject into tProperties
   end if
   
   if tProperties["name"] is empty then
      set the name of pObject to uuid()
      put the short name of pObject into tProperties["name"]
   end if
   
   if tType is not "stack" then
      write "name" & kPropertyNameSeparator & tProperties["name"] & kPropertySeparator & return to file pFile
      write "type" & kPropertyNameSeparator & tType & kPropertySeparator & return to file pFile
      if tType is not "card" then
         write "owner" & kPropertyNameSeparator & the abbreviated name of the owner of pObject & kPropertySeparator & return to file pFile
      end if
      if the script of pObject is not empty then
         put the script of pObject into tProperties["script"]
      end if
   else
      put the width of pObject into tProperties["width"]
      put the height of pObject into tProperties["height"]
   end if
   
   local tKeys
   put the keys of tProperties into tKeys
   sort tKeys
   
   local tTemplateProperties
   put GetTemplateProperties(tType) into tTemplateProperties
   repeat for each line tKey in tKeys
      
      switch
         case tProperties[tKey] is tTemplateProperties[tKey]
         case tKey is among the words of "name id altId layer defaultButton mark"
         case tKey contains "icon"
         case tKey contains "pattern"
         case tKey is among the words of "rect fileName" and tType is "stack"
         case tKey is "text" and tType is "image"
            next repeat
            break
      end switch
      
      write tKey & kPropertyNameSeparator to file pFile
      if tProperties[tKey] is an array then
         
         local tArrayKeys
         put the keys of tProperties[tKey] into tArrayKeys
         sort tArrayKeys
         repeat for each line tArrayKey in tArrayKeys
            if tProperties[tKey][tArrayKey] is tTemplateProperties[tKey][tArrayKey] then
               next repeat
            end if
            write return & kPropertyValueKeySeparator & tArrayKey & kPropertyValueElementSeparator to file pFile
            if the number of lines of tProperties[tKey][tArrayKey] > 1 then
               write return to file pFile
            end if
            write tProperties[tKey][tArrayKey] to file pFile
         end repeat
      else
         if the number of lines of tProperties[tKey] > 1 then
            write return to file pFile
         end if
         write tProperties[tKey] to file pFile
         
      end if
      write kPropertySeparator & return to file pFile
   end repeat
   
   local tChildID
   switch tType
      case "stack"
         repeat for each line tChildID in the cardIDs of pObject
            write  kObjectSeparator & return to file pFile
            SaveObject the long id of card id tChildID of pObject, pFile
         end repeat
         break
      case "card"
         repeat for each line tChildID in the childControlIDs of pObject
            write kObjectSeparator & return to file  pFile
            SaveObject the long id of control id tChildID of pObject, pFile
         end repeat
         break
      case "group"
         repeat for each line tChildID in the childControlIDs of pObject
            write kObjectSeparator & return to file  pFile
            SaveObject the long id of control id tChildID of pObject, pFile
         end repeat
         break
   end switch
end SaveObject

 constant kWidgetProps = "rect toolTip visible topColor textStyle bottomColor dropShadow backColor lockLoc foreColor cantSelect layerMode shadowColor opaque innerGlow ink showBorder textSize textFont disabled colorOverlay hiliteColor behavior borderColor innerShadow hiliteBorder outerGlow blendLevel hiliteFill traversalOn focusColor textAlign layer"

function GetTemplateProperties pType
   switch pType
      case "stack"
         return the properties of the templateStack
      case "card"
         return the properties of the templateStack
      case "group"
         return the properties of the templateStack
      case "image"
         return the properties of the templateStack
      case "graphic"
      case "widget" -- !dirty hack!
         return the properties of the templateStack
      case "button"
         return the properties of the templateStack
      case "field"
         return the properties of the templateStack
      case "player"
         return the properties of the templateStack
      case "scrollbar"
         return the properties of the templateStack
   end switch
end GetTemplateProperties

private command ResetTemplateProperties
   reset the templateStack
   reset the templateCard
   reset the templateGroup
   reset the templateImage
   reset the templateGraphic
   reset the templateButton
   reset the templateField
   reset the templatePlayer
   reset the templateScrollbar
end ResetTemplateProperties

function GetWidgetProperties pObject
   local theObjectA
   
   # default properties
   put the short name of pObject into theObjectA["name"]
   repeat for each word theProperty in kWidgetProps
      put the theProperty of pObject into theObjectA[theProperty]
   end repeat
   
   # widget properties
   local tWidgetProps
   export pObject to array tWidgetProps
   
   put tWidgetProps["$kind"] into theObjectA["kind"]
   put tWidgetProps["$state"] into theObjectA["state"]
   
   return theObjectA
end GetWidgetProperties

command CreateWidget pPropertiesA
   local tObject
   
   local tWidgetProps
   put pPropertiesA["state"] into tWidgetProps["$state"]
   put pPropertiesA["kind"] into tWidgetProps["$kind"]
   FixWidgetState tWidgetProps["$state"]
   
   import widget from array tWidgetProps
   put the long id of the last widget into tObject
   
   -- this won't work if the card isn't open!
   set the name of tObject to pPropertiesA["name"]
   if word 1 of tObjects[tIndex]["owner"] is "group" then
      relayer tObject to front of tObjects[tIndex]["owner"]
   end if
   
   return tObject
end CreateWidget


private command FixWidgetState @pState
   if pState is an array then
      repeat for each key tKey in pState
         FixWidgetState pState[tKey]
      end repeat
   else
      switch
         case pState is "true"
            put pState is "true" into pState
            break
         case pState is "false"
            put pState is "true" into pState
            break
         case pState is a number
            put pState * 1 into pState
            break
      end switch
   end if
end FixWidgetState
