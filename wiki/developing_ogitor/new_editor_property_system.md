<!--
title: New Editor Property System
template: wiki
order: 53
-->
== General ==
Ogitor features a powerful property system that streamlines exposing parameters of your editors to the Ogitor GUI. Ogitor then also automatically takes care of saving the values in the .OGSCENE file without additional coding to do.
----
== Guide ==
The following section describes the needed steps:

**1.** Let your editor class inherit from CBaseEditor
|:pre code cpp:|{{{
class MyEditor : public CBaseEditor
}}}
**2.** Declare the parameters you want to use as memeber variables of your editor class:
|:pre code cpp:|{{{
OgitorsProperty         *mSpeed;
OgitorsProperty      *mDirection;
}}}
**3.** Define a (private / protected) member method for each parameters that will later automatically get called once the parameter value is changed via the GUI.
|:pre code cpp:|{{{
bool _setSpeed(OgitorsPropertyBase* property, const Ogre::Real& value);
bool _setDirection(OgitorsPropertyBase* property, const Ogre::Vector2& value);
}}}

**4.** Create a factory class for your editor and let it inherit from CBaseEditorFactory
|:pre code cpp:|{{{
class MyEditorFactory : public CBaseEditorFactory
}}}

**5.** In the constructor of your editor factory, declare all the parameters you want to see in the GUI later. For details see the source code comments.
|:pre code cpp:|{{{
MyEditorFactory::MyEditorFactory(OgitorsView *view) : CBaseEditorFactory(view)
{
    // Some general settings like name, type and icon of your editor
    mTypeName = "Editor Name";
    mEditorType = ETYPE_GENERALPURPOSE;
    mAddToObjectList = true;
    mIcon = "icon.svg";
    mCapabilities = CAN_DELETE;

    OgitorsPropertyDef * definition = 0;

    // Add a new property with the ID "general::speed", which will be displayed in the parameter group "General" and with the label "Speed" along with the tool tip "Speed of object".
    definition  = AddPropertyDefinition("general::speed", "General::Speed", "Speed of object", PROP_REAL);
    // Set min, max and step size, always as Ogre::Any and inside it the type of the property!
    definition->setRange(Ogre::Any(0.0f), Ogre::Any(25.0f), Ogre::Any(0.1f));
    definition = AddPropertyDefinition("general::direction", "General::Direction", "", PROP_VECTOR2);
    definition->setStepSize(Ogre::Any(Ogre::Vector2(1.5f, 5.0f)));
    
    // You usually don't want the name and layer to be editable, yet still displayed in the parameter list, so disable them
    OgitorsPropertyDefMap::iterator it = mPropertyDefs.find("name");
    it->second.setAccess(true, false);

    it = mPropertyDefs.find("layer");
    it->second.setAccess(false, false);
}
}}}

**6.** In the //CreateObject(...)// method that you have to override in your editor factory class, you have to at some point call the method //createProperties(OgitorsPropertyValueMap ¶ms)// of your editor class, which should look something like this:
|:pre code cpp:|{{{
void MyEditor::createProperties(OgitorsPropertyValueMap ¶ms)
{
    // Creates the link between the parameters GUI element and your declared callback function.
    // First parameter is the class member you declared in step #2, then the parameter ID as defined in step #5, followed by type, default value and a tag (used as an index in case the parameter is used multiple times, e.g. the EntityEditor has many material parameters, one for each Ogre::SubEntity). The last part is the link to the setter function which comprises of parameter type, the class the value change callback function is in plus the actual callback function name.
    PROPERTY_PTR(mSpeed, "general::speed", Ogre::Real, 10.0f, 0, SETTER(Ogre::Real, MyEditor, _setSpeed));
    PROPERTY_PTR(mDirection, "general::direction", Ogre::Vector2, Ogre::Vector2(0.0f, 0.0f), 0, SETTER(Ogre::Vector2, MyEditor, _setDirection));

    // Mandatory call for initialization
    mProperties.initValueMap(params);
}
}}}

**7.** Implement your call back method that your defined in step #3 to react tot he new parameter value.
----
**Note:** As usual with Ogitor, it immensely helps to have a look at the source code, in this case especially already existing editors.