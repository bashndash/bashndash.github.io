# Java Problems and Solutions
a collection of samples

## Table of Contents
- [Convert an integer into binary representation](#convert_to_binary)
- [Read a text file line by line using NIO](#read_text_file_nio)

## Convert an integer into binary representation
```java
int number = 21;
String binaryRepresentation = Integer.toBinaryString( number );
```

## Read a text file line by line using [NIO](https://docs.oracle.com/en/java/javase/18/core/java-nio.html)
```java
Path path = Paths.get( "C:/log/sample.txt" );
try
{
  Files.lines( path ).forEach( System.out::println );
}
catch( IOException e )
{
  e.printStackTrace();
}
```

