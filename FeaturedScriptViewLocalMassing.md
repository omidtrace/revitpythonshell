# Introduction #

This is a script [Chen Kianwee](http://chenkianwee.wordpress.com/) kindly sent me:

> I did this very simple script using revit python shell, it was really  useful for exploring revit api as I can quickly see the results without  closing and opening revit. Attached is the .py file I wrote, hope its ok.


# Code #

```
import clr 
clr.AddReference('RevitAPI')
import Autodesk.Revit.DB
import Autodesk.Revit.DB.Architecture
from Autodesk.Revit.DB.Architecture import Room
from Autodesk.Revit.DB import Wall

doc = __revit__.ActiveUIDocument
document = doc.Document
#setup a collector
plcollector = Autodesk.Revit.DB.FilteredElementCollector(document)
#get all the property lines
property_line = plcollector.OfClass(Autodesk.Revit.DB.PropertyLine).ToElements()

property_name = "plot17" #change this parameter to get different property lines
building_name = "building17"

for pl in property_line:
        name = pl.ParametersMap.get_Item("Name").AsString()
        if name == property_name:
                plot_area = pl.ParametersMap.get_Item("Area").AsDouble()
                print name, "I am active and I am ", plot_area, "m2"

masscollector = Autodesk.Revit.DB.FilteredElementCollector(document)
mass = masscollector.OfCategory(Autodesk.Revit.DB.BuiltInCategory.OST_Mass).ToElements()

for m in mass:
        mname = m.Name.ToString()
        if mname == building_name:
                gfa = m.get_Parameter(Autodesk.Revit.DB.BuiltInParameter.MASS_GROSS_AREA)
                if gfa:
                        gfa = m.get_Parameter(Autodesk.Revit.DB.BuiltInParameter.MASS_GROSS_AREA).AsDouble()
                        print gfa
```