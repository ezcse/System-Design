# Multiple Level Cache Management System

This is a C++ implementation of a Multiple Level Cache Management System. The cache system consists of multiple levels (L1, L2, L3, ..., Ln) where each level stores key-value pairs of data. Each level has its own capacity, read time, and write time.

## Features

Supports multiple levels of cache with different capacities, read times, and write times.
#### 1. READ KEY Operation:
* Read will start from L1 level. 
    * If Key is found at this layer then this value will be returned.
    * If Key is not found at this layer then try to read from next layer. 
    * Keep doing this until the value of KEY is found at any level, or the last level has been reached. 
* If the value of KEY is found at any level, then this VALUE should also be written into all previous levels of cache which have higher priority that this level. 
* Total Read time is the sum of Read times of all levels where READ operation was attempted and Write time of all levels where WRITE operation was attempted. 
* You have to print the VALUE of KEY, and the total TIME taken to read it.

#### 2. WRITE KEY Operation:
* Write will start from L1 level. 
    * Write the value of KEY at this level and all the levels below it.
    * If at any level, value of KEY is same as given VALUE then don't write again and return. 
* Total Write time is the sum of Write times of all levels where WRITE operation was attempted and Read time of all levels where READ operation was attempted. 
* You have to print the total TIME taken to write this KEY-VALUE.


## Solution

To implement a Multiple Level Cache Management System in C++ as described, we'll need to define classes for the Cache and CacheSystem. Each cache level will store key-value pairs, and the system will manage operations across multiple cache levels. Let's start by outlining the classes and methods needed for this implementation.

### Classes and Methods

1. **CacheLevel**: Represents a single level of cache.
    - `unordered_map<string, string> storage`: To store key-value pairs.
    - `int capacity`: Maximum number of elements the cache level can hold.
    - `int readTime`: Time taken to read from this cache level.
    - `int writeTime`: Time taken to write to this cache level.
    - Methods:
        - `CacheLevel(int capacity, int readTime, int writeTime)`: Constructor to initialize the cache level.
        - `bool contains(const string& key)`: Checks if the cache contains a key.
        - `string read(const string& key)`: Gets the value for a key.
        - `void write(const string& key, const string& value)`: Puts a key-value pair into the cache.
        - `void getReadTime(const string& key)`: Gets the read time for this cache.
        - `bool continas(const string&)`: Checks if the cache is contains the key.

2. **CacheSystem**: Manages multiple cache levels.
    - `vector<CacheLevel> levels`: A list of cache levels.
    - Methods:
        - `CacheSystem(vector<pair<int, pair<int, int>>> cacheConfig)`: Constructor to initialize cache levels.
        - `void readKey(const string& key)`: Reads a key from the cache system.
        - `void writeKey(const string& key, const string& value)`: Writes a key-value pair to the cache system.
        
### Implementation

Here's a possible implementation in C++:

```cpp


#include <iostream>
#include <string>
#include <unordered_map>
#include <vector>
#include <utility>
#include <list>

using namespace std;

class CacheLevel{
	
private:
	int capacity;
	int readTime;
	int writeTime;
	list<string> order;
	unordered_map<string, string> storage;

public:
	CacheLevel(int size, int rTime, int wTime){
		capacity = size;
		readTime = rTime;
		writeTime = wTime;
	}	

	void write(const string&  key, const string& value)
	{
		if(storage.size() >= capacity){
			string olderKey = order.front();
			order.pop_front();
            storage.erase(olderKey);
		}
		if (storage.find(key) == storage.end()) {
            order.push_back(key);
        }
		storage[key] = value;
	}

	string read(const string&  key)
	{
		return storage[key];
	}

	bool contains(const string&  key)
	{
		return storage.find(key) != storage.end();
	}

	int getReadTime(){
		return readTime;
	}

	int getWriteTime(){
		return writeTime;
	}
	
	void printCache(){
	    for(auto itr = order.begin(); itr!=order.end(); itr++)
	        cout<<"{"<<*itr<<"}, ";
	    cout<<endl;
	}
};

class CacheSystem{

private:
	vector<CacheLevel *> levels;


public:
	CacheSystem(vector<pair<int,pair<int,int> > > cacheConfig)
	{
		for(auto& itr : cacheConfig)
		{
			int capacity = itr.first;
			int readTime = itr.second.first;
			int writeTime = itr.second.second;
			levels.push_back(new CacheLevel(capacity, readTime, writeTime));
		}
	}

	void readKey(const string& key)
	{
		int totalReadTime = 0;
		bool keyFound = false;
		string valueForKey = "";
		int itr = 0;

		for(; itr<levels.size(); itr++)
		{
			totalReadTime += levels[itr]->getReadTime();

			if(levels[itr]->contains(key))
			{
				keyFound = true;
				valueForKey = levels[itr]->read(key);

				for(int prevItr = itr-1; prevItr >=0; prevItr--)
				{
					levels[prevItr]->write(key, valueForKey);
					totalReadTime = totalReadTime + levels[prevItr]->getWriteTime();
				}
                break;
			}
		}

		if(keyFound == true)
			cout<<"Key is found in chache level: "<<itr<<". Total read time is:"<<totalReadTime<<endl;

		else
			cout<<"Key is not found in chache. Total read time is:"<<totalReadTime<<endl;

		return ;
	}

	void writeKey(const string&  key, const string&  value)
	{
		int totalWriteTime = 0;

		for(int i=0; i<levels.size(); i++)
		{
			if(levels[i]->contains(key))
			{
				if(levels[i]->read(key) == value)
					break;
			}

			levels[i]->write(key, value);
			totalWriteTime += levels[i]->getWriteTime();
		}

		cout<<"total time for write operation is:"<<totalWriteTime<<endl;

		return;
	}
	
	void printCache()
	{
	    cout<<"------------------------"<<endl;
	    for(int i=0; i<levels.size(); i++)
	    {
	        cout<<"level:"<<i<<" => ";
	        levels[i]->printCache();
	    }
	    cout<<"------------------------\n"<<endl;
	}

};



int main() 
{
    vector<pair<int, pair<int, int> > > cacheConfig;

    cacheConfig.push_back({2, {3, 1}});
    cacheConfig.push_back({3, {3, 2}});
    cacheConfig.push_back({4, {3, 3}});
   

    CacheSystem cacheSystem(cacheConfig);

    cacheSystem.writeKey("A", "1");
    cacheSystem.writeKey("B", "2");
    cacheSystem.writeKey("C", "3");

    cacheSystem.printCache();
    
    cacheSystem.readKey("A");
    cacheSystem.readKey("C");
    cacheSystem.readKey("B");
    cacheSystem.readKey("D");
    
    cacheSystem.printCache();
    cacheSystem.writeKey("A", "4");

    cacheSystem.readKey("A");

    return 0;
}



```

### Explanation

1. **CacheLevel Class**:
    - **contains**: Checks if a key is present in the cache.
    - **read**: Returns the value associated with a key.
    - **write**: Inserts a key-value pair into the cache, evicting the oldest entry if the cache is full.
    - **getReadTime**: Returns the read time for this cache level.
    - **getWriteTime**: Returns the write time for this cache level.

2. **CacheSystem Class**:
    - **readKey**: Attempts to read a key from the highest priority cache (L1) down to the lowest (Ln). If found, it updates all higher-priority caches with the value and prints the total time taken.
    - **writeKey**: Writes a key-value pair to all cache levels from L1 to Ln, stopping if it encounters an existing entry with the same value.

This implementation uses a simple FIFO eviction policy for cache levels and handles read and write operations as described.
