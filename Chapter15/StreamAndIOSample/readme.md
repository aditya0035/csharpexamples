#### .Net Stream Architecture
Concepts
  > **Backing Store** : A source form which bytes can be sequentially read or A source to which bytes cam be sequentially written
  > e.g. file, network connection etc.
  

  
>**Stream** : .Net class which exposes set of methods for writing, reading and positioning to interact with backing store.
>stream deals with data serially, either one byte at a time or in block of manageable size irrespective of size of backing store.


>**Backing store stream** : hardwired to a particular type of backing store e.g. FileStream or NetworkStream

>**Decorator Stream** : These are both writable and readable stream i.e feed off another stream transform the data and then return a new stream of data.
>e.g GzipStream (Data Compression stream), CryptoStream etc.

>**Stream Adapters** : A stream (Decorator or Backing Store Stream) takes byte input and output byte output, to read and write data types
>like string, integers or XML elements we need Adapters which will do this work of converting data type to bytes.
>e.g. Text adapters - for String and Character Data , Binary adapters -for primitive types such as int, bool, string and float, 
>Xml adapters - for XML. 

![Stream architecture](https://www.oreilly.com/library/view/c-40-in/9781449379629/httpatomoreillycomsourceoreillyimages499313.png)
Reference: [C# 8.0 in a Nutshell: The Definitive Reference](https://www.amazon.com/gp/product/1492051136?ie=UTF8&tag=cinanu-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=1492051136)

*Decorator Stream Benefits*
* Can chain multiple decorator stream for transformation
* Connect at runtime

Important Points:
* Both backing store and decorator streams deals in bytes
* Adapter stream wraps a stream just like decorator
* Adapter is not itself stream, its wrap a stream with specialized method to deal with particular type and then write bytes to the wrapped stream

Note: To wrap stream we simply pass one object into another constructor.

###### Stream is abstract base class for all stream.

##### Using Stream
**Reading & Writing**
  * Read-Only Stream: if **CanWrite** return false.
  * Write-Only Stream: if **CanRead** Return false.
>Read Method: Receive block of data from stream into array & returns no of byte received, these no of bytes can be <= length of array
  > * no of bytes received<length => either end of stream or stream data chunks is smaller then array length
	so balance bytes in array will remain unwritten, will contains old value.
>>*Correct way to read 1000 byte stream*:
``` csharp
byte[] buffer=new byte[1000];
int bytesRead=0;
int chunkSize=1;
while(bytesRead<buffer.length && chunkSize>0{
	chunkSize=stream.Read(data,bytesRead,data.length-bytesRead);
	bytesRead=bytesRead+chunkSize;
}
#Alternate way.
byte[] data=new BinaryReader(stream).ReadBytes(1000);
# for Seekable Stream
byte[] data=new BinaryReader(stream).ReadBytes((int)stream.length);
```
>Note:
>>* The ReadBytes method return -1 (integer) to indicate end of stream.
>>* Write & WriteByte send data to stream.
>>* In Read & Write methods, offset represent index of array from where reading or writing begin. 

**Seeking**
  * Seekable Stream: if **CanSeek** return true.
	> Benefits of Seekable stream
    > * Can query and modify length (SetLength).
    > * Change position of reading & writing (Position).
    >> Note: Position property is relative to beginning but Seek Method allow to move relative to current position or end of stream.

**Closing and Flushing**
  * Dispose stream after use, so to release underlying file handle or socket handle.
  * Use Using block for auto dispose of stream.
  > Note:
  > * Dispose & close are identical
  * Closing Decorator stream closes both decorator and backing store stream i.e with chain of decorator outermost stream dispose will close all underlying stream.
  >Note:
  > * Some stream internally maintain buffer data (to and from the backing store) for better performance by avoiding round trip 
  > so it writing to stream delayed till buffer got filled
  > * Flush Method: Forces internally buffered data to be written immediately. e.g. stream.Flush(); stream.Close();
  > * Closing Or Dispose automatically call Flush.

**Timeouts**
* CanTimeout=true => stream support read and write timeout e.g. Network Stream
* ReadTimeout & WriteTimeout represent timeout in ms, 0 => no timeout
* ReadAsync/WriteAsync do not support timeouts but can be achieved using cancellation token

**Thread Safety**
* Stream are not thread safe
* Workaround is via static Synchronized method, take stream as input and return tread safe wrapper.Wrapper 
obtain exclusive lock around read, write & seek make sure only single thread can perform operation at a time.

**Backing Store Stream**





