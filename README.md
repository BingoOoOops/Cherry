# Cherry: A Data Series Index with Efficient Writing and Low-Latency Querying

## Abstract: 

Applications constantly generate and collect large data series, and analysts require real-time data series similarity search to meet the demands of data analysis applications. However, even state-of-the-art works are unable to meet the performance requirements of many scenarios. We propose Cherry, an index with extremely fast write performance and superior query capability through careful design. We present a novel index structure, ZSBtree, that draws inspiration from the combined features of Z-order curve and Symbolic Aggregate approXimation (SAX), to efficiently merge the clustering ability of SAX with the performance of B+Tree. With the aid of a specially designed heuristic bulk-loading algorithm, the ZSBtree exhibits remarkable pruning capability and performance without incurring any additional time cost. To achieve high writing and query processing efficiency, we further integrate ZSBtree and Log-Structured Merge Tree (LSM), and specifically design parallel and heuristic algorithms for them. Additionally, we leverage the parallel capabilities of modern hardware to accelerate expensive vector operations in our algorithm. Compared with the current fastest multi-core architecture work on a 1 billion dataset, Cherry utilizes two orders of magnitude less memory and half the index size, achieves an index construction speed that is 0.42x faster, and exact query speed that is 10.8x faster.

# 1. Project Structure

## Code
This folder contains the code for implementing Cherry.

## Data
This folder contains the code for the synthetic dataset generation, as well as the oneDrive links to the real dataset.
These datasets are used in the construction and querying of cherry in paper experiments.

## Paper
This folder contains the pdf of the Cherry.



# 2. Requirements

Ubuntu 20.04 \
GCC 9.4.0 \
JDK 8 \
Boost 1.81.0 \
Tcmalloc (libunwind 1.1 and gperftools 2.1)

The data is better stored on SSD, otherwise IO time will overlap CPU time and will not reflect Cherry's superior performance.


# 3. Build and Run
Cherry is written on the basis of [leveldb](https://github.com/google/leveldb), mainly cmake projects (in `leveldb_sax`), and provides jni interfaces for java calls.
The compilation options for the c++ part of the code are in `/leveldb_sax/CMakeLists.txt`.

We recommend running with Idea and Clion.
Compile `leveldb_sax` with Clion to generate `libleveldbj.so` and add its path to the VM options of java.
For example: `-Djava.library.path=./leveldb_sax/cmake-build-release`. Then you can run it in the Idea.
You can find Idea's VM options settings in: top right corner select `Edit Run/Debug configurations` -> `alt+v`

Of course, If you don't want to install Idea and Clion, it can also be compiled and run only using the following command：
```sh
# 1. Compile leveldb_sax
mkdir -p leveldb_sax/build
cd leveldb_sax/build
cmake -DCMAKE_BUILD_TYPE=Release .. 
cmake --build . --target leveldbj -j 3

# 2. Compile the java part
cd ../.. && mkdir -p out
javac -d out -cp "lib/*" -sourcepath src/main/java src/main/java/com/cherry/Main.java src/main/java/leveldb_sax/db_send.java

# 3. Run Cherry
java -cp out:"lib/*"  -Djava.library.path=leveldb_sax/build/  com.cherry.Main
```

Please make sure that both `inputPath` and `queryFilePath` have files, see the Parameter for specific settings. 

Enter 0 or 1 in the console to select the approximate or exact query, and then enter the number of queries and the k value of k-nn.
The program will automatically build index and perform queries.

# 4. Parameter
Our parameters are set in the following two files.
1. `leveldb_sax/sax/include/globals.h`
2. `src/main/java/com/cherry/Parameters.java`

Please update it to the appropriate setting by yourself before running.

Note that some parameters in these two files are corresponding and need to be modified together.