# Introduction #

This is the source of a module I use to make certain operations with the RevitAPI easier to access from IronPython. This will probably grow as time goes on and eventually will be part of an Installer mechanism for sharing useful RevitPythonShell scripts.

It expects the presense of the [geomutil module](ModuleGeomutil.md).

# Details #

Copy this code into a file called `revitutil.py` in your search path (e.g. `C:\RevitPython\revitutil.py`:

```
'''
revitutil.py

a collection of utillity functions / stuff for working with
revit in the RevitPythonShell.
'''

# add references to needed assemblies
import clr
clr.AddReference('System')
clr.AddReference('mscorlib')
clr.AddReferenceToFileAndPath(r'C:\Program Files\Autodesk Revit Architecture 2010\Program\RevitAPI.dll')

# import some stuff from those assemblies
import Autodesk.Revit
import System
from System.Collections.Specialized import *
from System.Collections.Generic import *

import geomutil

def meters(feet):
    return float(feet) * 0.3048

def pointMeters(p):
    return geomutil.Point(meters(p.x), meters(p.y), meters(p.z))


def lineFromCurve(curve):
    '''a revit curve is returned as a line, conversion between feet and meters'''
    p1 = geomutil.Point(curve.EndPoint[0].X, curve.EndPoint[0].Y, curve.EndPoint[0].Z)
    p2 = geomutil.Point(curve.EndPoint[1].X, curve.EndPoint[1].Y, curve.EndPoint[1].Z)
    return geomutil.Line(pointMeters(p1), pointMeters(p2))

class Document(object):
    '''
    Encapsulates a Autodesk.Revit.Document object and adds
    certain methods.
    '''
    def __init__(self, doc):
        self.doc = doc
    
    def listRooms(self):
        '''returns a list of room objects'''
        roomElements = self.listElementsByType(Autodesk.Revit.Elements.Room)
        return [Room(r) for r in roomElements]

    def listRoofs(self):
        '''returns a list of roof objects'''
        return [Roof(r) for r in self.listElementsByType(Autodesk.Revit.Elements.FootPrintRoof)]

    def listElementsByType(self, T):
        elements = List[Autodesk.Revit.Element]()
        factory = self.doc.Application.Create.Filter
        filter = factory.NewTypeFilter(T)
        self.doc.get_Elements(filter, elements)
        return list(elements)

    def __getattr__(self, name):
        '''try looking in the encapsulated object'''
        return self.doc.__getattribute__(name)

class Roof(object):
    '''
    Encapsulates an Autodesk.Revit.Elements.FootprintRoof object and
    adds some utility methods.
    '''
    def __init__(self, roof):
        self.roof = roof

    def GetOverhang(self, curve):
        try:
            return self.roof.get_Overhang(curve)
        except:
            return 0.0

    def GetSlope(self, curve):
        try:
            return self.roof.get_Slope(curve)
        except:
            return 0.0

    def GetSlopeAngle(self, curve):
        try:
            return self.roof.get_SlopeAngle(curve)
        except:
            return 0.0

    def __getattr__(self, name):
        '''try looking in the encapsulated object'''
        try:
            return self.roof.__getattribute__(name)
        except:
            print "Error retrieving property %s on roof %s" % (name, self.Id.Value)
            return None
    def __str__(self):
        return "Roof(%s)" % self.room.Id.Value
    def __repr__(self):
        return "Roof(%s)" % self.room.Id.Value


class Room(object):
    '''
    Encapsulates a Autodesk.Revit.Elements.Room object and adds some
    utility methods.
    '''
    def __init__(self, room):
        self.room = room

    def getWalls(self):
        '''returns all revit walls that are in contact with the room'''        

    def getBoundingBox(self):
        '''returns the BoundingBoxXYZ of the model'''
        bb = self.room.get_BoundingBox(None)        
        return ((bb.Max.X, bb.Max.Y, bb.Max.Z), (bb.Min.X, bb.Min.Y, bb.Min.Z))

    def __getattr__(self, name):
        '''try looking in the encapsulated object'''
        try:
            return self.room.__getattribute__(name)
        except:
            print "Error retrieving property %s on room %s" % (name, self.Id.Value)
            return None
    def __str__(self):
        return "Room(%s)" % self.room.Id.Value
    def __repr__(self):
        return "Room(%s)" % self.room.Id.Value

class Wall(object):
    '''
    Encapsulates a Autodesk.Revit.Elements.Wall object and adds some 
    utility methods.
    '''
    def __init__(self, wall):
        self.wall = wall

    def getWidth(self):
        return meters(self.wall.Width)

    def getHeight(self):
        return meters(self.wall.get_BoundingBox(None).Max.Z) - meters(self.wall.get_BoundingBox(None).Min.Z)

    def getLine(self):
        '''returns the walls location curve as a geomutil.Line'''
        location = Autodesk.Revit.LocationCurve.Curve.__get__(Autodesk.Revit.Element.Location.__get__(self.wall))
        startPoint = geomutil.Point(location.EndPoint[0].X, location.EndPoint[0].Y, location.EndPoint[0].Z)
        endPoint = geomutil.Point(location.EndPoint[1].X, location.EndPoint[1].Y, location.EndPoint[1].Z)
        return geomutil.Line(pointMeters(startPoint), pointMeters(endPoint))

    def __getattr__(self, name):
        try:
            return self.wall.__getattribute__(name)
        except:
            print 'Error retrieving property %s on wall %s' % (name, self.UniqueId)
            return None

def removeParameters(parameterFile, document):
    '''removes all parameters in parameterFile from the document (unbinds them)'''
    document.Application.Options.SharedParametersFilename = parameterFile
    df = document.Application.OpenSharedParameterFile()
    for dg in df.Groups:
        for d in dg.Definitions:
            print "removing parameter", d.Name
            document.ParameterBindings.Remove(d)

```