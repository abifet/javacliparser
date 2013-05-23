javacliparser
=============

Java Command Line Interface Parser is a library to parse command line options that is able to use nested options.

It allows to enter options with suboptions that define a command line tree, for example:

`` DoTask -s 5 -l (Task1 -s 3 -l (Task2 -s 1)) ``

The code to implement this, is the following:

```java
public class DoTask implements Configurable {
   public IntOption sizeOption = new IntOption("size", 's',
            "Size", 10, 1, Integer.MAX_VALUE);

   public ClassOption taskClassOption = new ClassOption("taskClass", 'l',
            "Task to execute.", Task1.class, "Task1 -s 4");
      
   public void init() {
        Task1 task = this.taskClassOption.getValue();
        task.init()
   }
    public static void main(String[] args) throws Exception {
        DoTask main = ClassOption.createObject(args, DoTask.class);
        main.init();
    }   
}

public class Task1 implements Configurable {
   public IntOption sizeOption = new IntOption("size", 's',
            "Size", 10, 1, Integer.MAX_VALUE);
    
    public ClassOption taskClassOption = new ClassOption("taskClass", 'l',
            "Task to execute.", Task2.class, "Task2 -s 100");

    public void init() {
        Task2 task = this.taskClassOption.getValue();
        task.init();
    }
}

public class Task2 implements Configurable {
    public IntOption sizeOption = new IntOption("size", 's',
            "Size", 10, 1, Integer.MAX_VALUE);

    public void init() {
    //...
    }
}

```

Using the library is as easy as this:

   1. Create a class that implements Configurable
   2. Add option objects as fields inside the class
   3. To retrieve the value of the options use the option method ``getValue()``
   4. To create a new object use ``ClassOption.createObject()``

The following types of options are supported:

   * Integer: ``IntOption (name, char, purpose, default Value, min Value, max Value)``
   * Float: ``FloatOption (name, char, purpose, default Value, min Value, max Value)``
   * Flag: ``FlagOption (name, char, purpose)``
   * File: ``FileOption (name, char, purpose, default File name, default File extension, Output)`` 
   * String: ``StringOption (name, char, purpose, default Value)``
   * Multichoice: ``MultichoiceOption (name, char, purpose, option labels, option descriptions, default option index)``
   * Class: ``ClassOption (name, char, purpose, required type, default CLI string, null String)`` 
   * List: ``ListOption (name, char, purpose, expected option type, default list, separator char)`` 
 
Example of usage:

```java
public class Task implements Configurable {
    public IntOption gracePeriodOption = new IntOption(
            "gracePeriod",
            'g',
            "The number of instances a leaf should observe between split attempts.",
            200, 0, Integer.MAX_VALUE);

    public ClassOption splitCriterionOption = new ClassOption("splitCriterion",
            's', "Split criterion to use.", SplitCriterion.class,
            "InfoGainSplitCriterion");

    public FloatOption splitConfidenceOption = new FloatOption(
            "splitConfidence",
            'c',
            "The allowable error in split decision, values closer to 0 will take longer to decide.",
            0.0000001, 0.0, 1.0);

    public FlagOption binarySplitsOption = new FlagOption("binarySplits", 'b',
            "Only allow binary splits.");
            
    public FileOption dumpFileOption = new FileOption("dumpFile", 'd',
            "File to append intermediate csv results to.", null, "csv", true);
            
    public StringOption xTitleOption = new StringOption("xTitle", 'm',
            "Title of the plots' x-axis.", "Processed instances");  

    ....
}
```

This library is used in the SAMOA and MOA projects for mining big data streams.
