## Table of Contents
<!-- TOC -->
  * [Table of Contents](#table-of-contents)
* [Java Problems and Solutions](#java-problems-and-solutions)
  * [Convert an integer into binary representation](#convert-an-integer-into-binary-representation)
  * [Read a text file line by line using NIO](#read-a-text-file-line-by-line-using-nio)
  * [Create SHA-256 checksum of a file](#create-sha-256-checksum-of-a-file)
  * [Write random bytes to a file](#write-random-bytes-to-a-file)
<!-- TOC -->

# Java Problems and Solutions
a collection of samples

## Convert an integer into binary representation
```java
int number = 21;
String binaryRepresentation = Integer.toBinaryString( number );
```

## Read a text file line by line using NIO
See [Java-NIO](https://docs.oracle.com/en/java/javase/18/core/java-nio.html) for further information.
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

## Create SHA-256 checksum of a file
```java

  public static void main(String[] args)
  {
    Path path = Paths.get( "C:/log/sample.txt" );
    dumpChecksum( path );
  }

  private static void dumpChecksum( Path path )
  {
    try( InputStream is = Files.newInputStream( path, StandardOpenOption.READ ) )
    {
      MessageDigest shaDigest = MessageDigest.getInstance( "SHA-256" );

      //byte array to read data in chunks
      byte[] byteArray = new byte[1024];
      int bytesCount = 0;

      //read data and update digest until file ends
      while( (bytesCount = is.read( byteArray ) ) != -1 )
      {
        shaDigest.update(byteArray, 0, bytesCount);
      };

      // get digest as byte array
      byte[] digest = shaDigest.digest();

      StringBuilder sb = new StringBuilder();
      for( byte b : digest )
      {
        // turn (signed) byte into unsigned byte (0-255)
        int unsignedInt = Byte.toUnsignedInt( b );

        // convert to a string representing a hex number ("0"-"ff")
        String hexString = Integer.toHexString( unsignedInt );

        // append leading zero if necessary
        if( unsignedInt < 10 )
        {
          sb.append( "0" );
        }

        // convert signed integer to hex (00-FF)
        sb.append( hexString.toUpperCase( Locale.ROOT ) );
      }

      System.out.println( sb.toString() );
    }
    catch( IOException e )
    {
      e.printStackTrace();
    }
    catch( NoSuchAlgorithmException e )
    {
      e.printStackTrace();
    }
  }
```
## Write random bytes to a file
In this example we fill a byte array with arbitrary random data which is then wrapped into
the byte buffer. The byte buffer is written to a file via the channel. This example obviously
makes no sense at first glance but sometimes it can be very useful to create binary blobs with 
random content for testing purposes.
```java
Random rng = new Random( System.currentTimeMillis() );
byte[] bytes = new byte[1024];
rng.nextBytes( bytes );
ByteBuffer buffer = ByteBuffer.wrap( bytes );
try( WritableByteChannel channel = Files.newByteChannel( newPath, StandardOpenOption.CREATE_NEW ) )
{
  channel.write( buffer );
}
catch( IOException e )
{
  e.printStackTrace();
}
```
You can play with the buffer size. For creating many very large files in acceptable time the following approach can be used.
Note the usage of StandardOpenOption.APPEND.
```java
for( int i = 0; i < nFiles; i++ )
{
  Path newPath = createIndexedFilePath( this.basePath, i );
  deleteIfExistAndCreate( newPath );
  Random rng = new Random( System.currentTimeMillis() );
  for( int j = 0; j < nBlocks; j++ )
  {
    byte[] bytes = new byte[16384]; // use a bigger block size for performance
    rng.nextBytes(bytes);
    ByteBuffer buffer = ByteBuffer.wrap( bytes );
    try( WritableByteChannel channel = Files.newByteChannel( newPath, StandardOpenOption.APPEND ) )
    {
      channel.write( buffer );
    }
    catch( IOException e )
    {
      e.printStackTrace();
    }
  }
}
```

## Implementing a very simple stopwatch
This is useful for rough run time estimates. Feel free to refine the format of elapsed time.
```java
public class StopWatch
{
  private Instant start;
  private Instant stop;

  private boolean isRunning;

  public StopWatch()
  {
    this.stop = Instant.now();
    this.isRunning = false;
  }

  public void start()
  {
    this.start = Instant.now();
    this.stop = null;
    this.isRunning = true;
  }

  public void stop()
  {
    this.stop = Instant.now();
    this.isRunning = false;
  }

  public boolean isRunning()
  {
    return this.isRunning;
  }

  public String getFormattedTimeInSeconds()
  {
    double seconds = getElapsedSeconds();
    return String.format( "%.3f", seconds );
  }

  public Duration getDuration()
  {
    return doGetDuration();
  }

  public double getElapsedSeconds()
  {
    long nanos = doGetDuration().toNanos();
    return ( (double) nanos) / 1e9;
  }

  private Duration doGetDuration()
  {
    Duration duration = null;
    if( null == this.stop )
    {
      duration = Duration.between( this.start, Instant.now() );
    }
    else
    {
      duration = Duration.between( this.start, this.stop );
    }
    return duration;
  }
```


