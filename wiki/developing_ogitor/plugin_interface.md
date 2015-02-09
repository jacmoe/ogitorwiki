<!--
title: Plugin Interface
template: wiki
order: 52
-->
= Plugin Interface =

== Plugin Initialization ==

Ogitor uses a plugin system similar to OGRE. The application searches for dynamic link libraries (*.dll, *.so) in the "../Plugins/" folder and tries to activate them by checking the existence of and invoking the following function as long as the plugin is not disabled via the Ogitor preferences:

|:pre code cpp:|{{{
extern "C" bool PluginExport dllStartPlugin(void *identifier, Ogre::String& name); 
}}}
@param identifier: The unique identifier automatically given to this plugin, which will be used for further communication with OgitorsRoot @param name: This is the user friendly name supplied by the plugin, for user to identify it easily @return: The return value determines if the plugin initialized properly or not. If the initialization fails, the function must return false, otherwise true Below is an example implementation of the dllStartPlugin(...) function:
|:pre code cpp:|{{{
bool dllStartPlugin(void *identifier, Ogre::String& name)
{
    name = "MyPlugin";
    
    mPluginTab = new MyPluginTab();
    mPluginViewWidget = new MyPluginViewWidget();
    mPluginPrefsEditor = new MyPluginPrefsEditor();

    Ogitors::DockWidgetData dockWidgetData;
    dockWidgetData.mCaption = "ViewWidgetHeader";
    dockWidgetData.mHandle = mPluginViewWidget ;
    dockWidgetData.mIcon = ":/icons/icon.svg";
    dockWidgetData.mParent = DOCK_RESOURCES;
    
    Ogitors::TabWidgetData tabWidgetData;
    tabWidgetData.mCaption = "Tab for my awesome plugin";
    tabWidgetData.mHandle = mPluginTab ;

    Ogitors::PreferencesEditorData preferencesEditorData;
    preferencesEditorData.mCaption = "MyPlugin";
    preferencesEditorData.mIcon = ":/icons/icon.svg";
    preferencesEditorData.mSectionName = "myPluginPrefSection";
    preferencesEditorData.mHandle = mPluginPrefsEditor;
    OgitorsRoot::getSingletonPtr()->RegisterDockWidget(identifier,dockWidgetData);
    OgitorsRoot::getSingletonPtr()->RegisterTabWidget(identifier, tabWidgetData);
    OgitorsRoot::getSingletonPtr()->RegisterPreferenceEditor(identifier, preferencesEditorData);

    return true;
} 
}}}
Inside the dllStartPlugin(...) function, the developer can initialize whatever needed for this plugin to work. Also it is the function in which you need to register any kind of:
* Custom Editor Plugin Factories 
* Custom Serializers 
* Custom Toolbars 
* Custom Dock Widgets 
* Custom Preferences Editors 

For the registration functions to work, the first parameter passed to any registration function is the identifier parameter passed during initialization of the plugin.

== Plugin DeInitialization ==

The following function must be present for a plugin to be deinitialized (unloaded):

|:pre code cpp:|{{{
extern "C" bool PluginExport dllStopPlugin(); 
}}}

Again, the return parameter is true for successful deinitialization, otherwise false. One important note is: You don't need to unregister anything you registered, they will be taken care of by OgitorsRoot, but you must clean up whatever you allocated from plugin initialization until deinitialization. Another Example of initialization and deinitialization from DotScene Serializer plugin:
|:pre code cpp:|{{{
CDotSceneSerializer *DotSceneSerializer = 0;
//----------------------------------------------------------------------------
bool dllStartPlugin(void *identifier, Ogre::String& name)
{
    name = "Dot Scene Serializer Plugin";
    DotSceneSerializer = new CDotSceneSerializer();
    OgitorsRoot::getSingletonPtr()->RegisterSerializer(identifier, DotSceneSerializer);
    return true;
}
//----------------------------------------------------------------------------
bool dllStopPlugin(void)
{
    delete DotSceneSerializer;
    return true;
} 
}}}  

== What's your name? ==

Another mandatory function a plugin needs to have is the function: 

|:pre code cpp:|{{{
extern "C" bool PluginExport dllGetPluginName(Ogre::String& name); 
}}}

Its purpose is to offer a fast way to get the name of a plugin without doing anything else. So the implementation of that function could look like this.

|:pre code cpp:|{{{
bool dllGetPluginName(Ogre::String& name)
{
    name = "MyPlugin";
    return true;
} 
}}}  

== What are your preferences? ==

It is possible for plugins to have an own section in the Ogitor preferences dialog in order to change their behavior. If you want such an entry to need to register a preferences editor for your plugin as shown in the section covering the initialization of plugins. When registering, you have to pass a filled instance of Ogitors::PreferencesEditorData:
* mCaption: The label your preferences section will have in the dialog 
* mIcon: The icon that will be used in the preferences dialog for your plugin 
* mSectionName: The name of the desired registry folder where the settings are going to be be saved to. This folder will automatically be created as a subfolder of the Ogitor registry folder. It is probably the best to just use the plugin name here without any blanks.
* mHandle: Pointer to a class instance of your plugin's preferences editor, in the example case here an instance of "MyPluginPrefsEditor"

The class "MyPluginPrefsEditor" needs to inherit QWidget and Ogitors::PreferencesEditor. The latter has three methods that you need to overwrite according to your needs:
* void getPreferences(Ogre::NameValuePairList& preferences); 

This one is automatically called when saving the preferences settings. You just have to fill in your settings in the NameValuePairList where the eky of each pair is the name for the registry entry and the value is the value for that key:
|:pre code cpp:|{{{
void MyPluginPrefsEditor::getPreferences(Ogre::NameValuePairList& preferences)
{  
 preferences.insert(Ogre::NameValuePairList::value_type("myPluginValue01", 
 Ogre::StringConverter::toString(spinBox->value())));

 preferences.insert(Ogre::NameValuePairList::value_type("myPluginValue02",
 Ogre::StringConverter::toString(checkBox->isChecked())));
} 
}}}  

* void* getPreferencesWidget(); 

This method is called when attempting to display the preferences dialog and is therefore the place where you should prepare your GUI with the correct settings:
|:pre code cpp:|{{{
MyPluginPrefsEditor::getPreferencesWidget()
{
    QSettings settings;
    settings.beginGroup(getPrefsSectionName().c_str());

    spinBox->setValue(settings.value("myPluginValue01", 10).toUInt());
    checkBox->setChecked(settings.value("myPluginValue02", false).toBool());

    settings.endGroup();
    
    return this;
} 
}}}  

* bool applyPreferences(); 

This method is automatically called when the user tries to apply the settings. You can put in here anything that needs to be done in order to adjust the running Ogitor instance to comply with the preferences changes the user just made. IMPORTANT: You also have to ensure that your plugin's preferences editor sends the signal isDirty() as soon as some values have been changed. This will enable the Apply button. Best practise is therefore to connect every GUI element in your preferences editor with one and the same slot in your class that will emit the singal isDirty(). These connections are best established in the constructor of your editor.

== Notification system ==

The notification system for plugins is the same as for the core of Ogitor. There are a number of events that are triggered upon various user or application actions that you can register yourself as a listener. Register:

|:pre code cpp:|{{{
Ogitors::EventManager::getSingletonPtr()->connectEvent(EventManager::AFTER_SCENE_EXPORT, this, true, 0, true, 0, EVENT_CALLBACK(MyClass, onAfterSceneExport)); 
}}}
This code will register the class MyClass' as a listener to the after scene export event. Once this event is fired by Ogitor, the method onAfterSceneExport(Ogitors::IEvent* evt)'' will be called. Process event:
|:pre code cpp:|{{{
void MyClass::onAfterSCeneExport(Ogitors::IEvent* evt)
{
    AfterSceneExportEvent* asee = static_Cast(evt);
    /*** do stuff ***/
} 
}}}
Your handler method will be called and given the matching IEvent instance. You can then cast it to the matching type (if once handler method is registered to various events, you can get the current event type via evt->getType()) and then retrieve further data if the event provides some or react in any other way to the event.