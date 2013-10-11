for_each
===================

 - For Visual Dataflex
 - By Sean Bamforth 


Usage:
```
    for_each {type} {variable} in {parameter1:optional} {parameter2:optional} {parameter3:optional} {parameter4:optional} 
    end_for_each
```

e.g. 
```
    Showln "Data Dictionary - index.2"
    Showln "==========================================="
    for_each DataDictionary riRow in (oTest_DD(Self)) Index.2
        Showln (SerializeRowID(riRow)) ":" Test.Description
    end_for_each 
    Showln ""
```
    
for_each types available : 
===================

  - DataDictionary - All records in DataDictionary 
  - DataDictionaryRecnum - All records in DataDictionary. Return Recnum, not RowID 
  - StringChar - All characters in a string 
  - TextFile - All Lines in a File 
  - Array - All items in an Array (Don't know how welll this works)
  - QueuedDataDictionary - All records in DataDictionary. Doesn't lose position if cursor changes. 

Creating your own iterator. 
===================
To create your own for_each iterator, you need to do the following. 

1. Subclass cIterator. 
Naming is impoortant. The text between "c" and "Iterator" will be the type. 
So a type of "Range" will need a cRangeIterator.
```
    Class cRangeIterator is a cIterator
    End_Class
```
2. Create the following functions and procedures:

Construct Object - Add properties we need. In our range example, we'll need a start place an end place and a current place. 
```
    Procedure construct_object 
        Forward Send construct_object 
        property integer piStart 
        property integer piStop 
        property integer piPos
    End_Procedure
```
InitialiseObject contains up to 4 Parameters. 
```
    Procedure InitialiseObject integer iStart integer iEnd
        set piStart to iStart  
        set piStop to iStop 
    End_Procedure
```
First Item knows how to find the first item, and returns true or false dependent on if that item is available. 
```
    Function FirstItem Returns Boolean
        set piPos to (piStart(self)) 
        function_return ( (piPos(self)) > (piStop(self)) )
    End_Function 
```
NextItem knows how to find the next item, and returns true or false dependent on if that item is available. 
```
    Function NextItem Returns Boolean
        set piPos to (piPos(self)) + 1
        function_return ( (piPos(self)) > (piStop(self)) )
    End_Function 
```    
ItemValue returns the current item. This can be a recnum, and array item, a character, etc. 
```
    Function ItemValue Returns Variant
        function_return (piPos(self))
    End_Function
```
We use DestroyObject Rarely, but we use it. (See cTextFileIterator)
```
    Procedure Destroy_Object
        Forward Send Destroy_Object 
    End_Procedure
```

Using it.
===================


```
    integer iCount
    for_each Range iCount in 100 150 
        showln iCount 
    end_for_each 
```

Fin.
