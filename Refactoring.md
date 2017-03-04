# qTox refactoring Plan

List of the modules (should be a subprojects)
  * [`core`](#core-module)
  * [`widget`](#widget-module)
  * [`audio`](#audiovideo-module)
  * [`video`](#audiovideo-module)
  * [`network`](#network-module)
  * [`persistence`](#persistence-module)
  * [`platform`](#platform-module)
  * [`non-module`](#non-module)
  * [~~`chatlog`~~](#chatlog-module) (move to ui)
  * [`test`](#unit-tests) (should be added)


## Core module
Will interface with c-toxcore

### Added:
  * `Friend` (*moved from non-module*)
  * `Group` (*moved from non-module*)
  * `FriendList` (*moved from non-module*)
  * `GroupList` (*moved from non-module*)
  * `toxsave` (*moved from persistence*)
  
### Removed:
  * ~~`RecursiveSignalBlocker`~~ (*moved to non-module stuff*)
  * ~~`coredefines.h`~~ (*kill it*)
  * ~~`indexedlist.h`~~ (*kill it*)
  * ~~`cstring.h`~~ (*kill it*)
  * ~~`corestructs.h`~~ (*move ToxFile to CoreFile, make DhtServer it's own class?* agree **@sudden6 @Diadlo**)
  
### Old:
  * `ToxId`
  * `ToxPk`
  * `ToxCall`
  * `CoreAv` -> 'ToxAv' ?
  * `Core` -> `ToxCore` (name clash?)
  * `CoreFile` -> `ToxFile`


## Widget module 
Contains all the user interface stuff

Rename module to 'ui'? (agree: **@sudden6 @Diadlo**)

### Submodules:
  * `dialog` (`QDialog` inheritance)
    * `SetPasswordDialog`
    * `AboutUser`
    * `ActiveDialog`
    * `ContentDialog`
    * `LoadHistoryDialog`
    * `FriendRequestDialog`
    * `RemoveFriendDialog`
  * `form` (`GenericForm` inheritance)
    * `Settings`
  * `layout` (`QLayout` inheritance)
  * `widget` (`QWidget` inheritance)
  * `utils` (usefull parts used throughout the ui) (agree **@sudden6**, against **@Diadlo** (IMO, should be moved in widget module root))
  * <a name="chatlog-module">`chatlog`</a> (Moved from src root)


## Audio/Video module
Provides qTox audio input and output and notification sounds, provides Video input and output

Has to interface with:
 * `toxav`
 * `platform`
 * `widget` (for video display)


## Network module

### ToxMe

### AutoUpdate


## Persistence module
Most part of this module should have one instance, which should be injected in other classes (see Inversion of control)
How do we handle qTox upgrades? (how it handled now?)
  
### Settings:
What about using macros here (not everywere)?
Example: `SETTINGS_PROPERY(name) -> void setName(T name); T getName(); Q_PROPERTY(...)`

  * Settings interfaces: 
    * `I[Global]SettingsGeneral`
    * `I[Global]SettingsUI`
    * `I[Global]SettingsNetwork`
    * `I[Global]SettingsAudio`
    * `I[Global]SettingsVideo`
  * Settings implementations:
    * `SettingsFile`
    * `test/SettingsFake`
    * `SettingsDb` (for the future it would be interesting to store non global settings in the DB)
    
### History:
  * `IHistory`
  * `class DBHistory : public IHistory`

### FileTransfers:
  * `IFileTransfers`
  * `class DBFileFransfers : public IFileTransfers`

### DBStorage VS Separate implementation (??):
We can have one DB provider, which implement all persistence interfaces.
Or many DB providers, which implement own interface (see above)

Example: `class DBStorage : public IHistory, public IFileTransfers`  


## Platform module


## Non-module
  * `RecursiveSignalBlocker`
  * `IPC`
  * `Nexus`
  * `main.cpp`


# Timeline (milestones?)
Only specifies the order, not definite times

- [x] complete cmake transistion
- [ ] somewhere should be "merge ui/redesign"
  - WIP by @Diadlo
- [ ] make module == subproject
- [ ] reorder the files, as defined above, no code changes yet
- [ ] define Interface for Core, ie create clean Header files
  - started by @sudden6, see [General-Classdiagram](General.png) and
  [ToxEncrypt-Classdiagram](ToxEncrypt-Classdiagram.png)
- [ ] implement the new Core interface step by step
- [x] <a name="unit-tests">create unit-tests for Core</a>
  - [x] setup tests with ToxId as example
  - [x] setup tests with ToxPk
- [ ] create SettingsInterface
  - [ ] port from the existing settings implementation
  - [ ] implement test/FakeSettings (to use it in tests)
- [ ] start the UI stuff
