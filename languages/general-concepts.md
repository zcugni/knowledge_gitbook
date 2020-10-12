# General Concepts

## List vs Array

* Since array get a pre-allocated space of a defined size, each data of the array is next to each other in memory
  * This is why we can use the base address + index \(aka array\[x\]\) to get to the desired part
    * The index is multiplied by the size of the data's type
  * This is also why you can't just extend them
* List don't get pre-allocated, so the different data can be at different places in memory
  * Which is way we need a pointer to the next one, and why we need to run through each node
  * This is also why we can remove or add node whenever we want

## Global & static var

* They persist, wihich is w

