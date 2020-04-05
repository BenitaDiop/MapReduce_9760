# MapReduce

This repo provides two examples and a script to easily define and run mapper and reducer functions against Hadoop MapReduce.

Generally speaking, on the top level there are folders that must contain:

* mapper.py
* reducer.py
* sample directory with data to be copied in hdfs within docker container

__ENSURE THAT .py FILES HAVE `chmod +x` PERMISSIONS__
(This is hadoop requirement)

Of course, the idea is to add more folders that demonstrate different aggregations that can be achieved with MapReduce over different datasaets (that are available in the sample folder). The aspiration was to make something reusable quickly and cheaply.

## Usage

1. Download this repo
2. CD into this repo
3. Run the following

```
docker run \
  -v $(pwd):/usr/local/hadoop/py \
  -it sequenceiq/hadoop-docker:2.7.1 \
  /usr/local/hadoop/py/py_runner.sh grep
```
(notice the **grep** keyword at the end - corresponds to the folder **grep**!)

expected output:

```
foo	6
quux	4
```


Another example:

```
docker run \
  -v $(pwd):/usr/local/hadoop/py \
  -it sequenceiq/hadoop-docker:2.7.1 \
  /usr/local/hadoop/py/py_runner.sh count
```
(notice the **count** keyword at the end  - corresponds to the folder **count**!)

expected output:

```
bar	0
foo	6
labs	0
quux	4
```






*Question*: How would you update the simple grep above to manage __any__ type of search? (In this case it encodes the "f" / "x" searching inside the reducer function). So basically, 

- **what if I wanted to find all the words that have "oo"** 
*Given that our data has the following text 'quux bar foo labs quux foo foo' if wanted to filter the words that merely the duplicated letters "oo" we can run the following command*

`INPUT`
```
docker run \
-v $(pwd):/usr/local/hadoop/py  \
-it sequenceiq/hadoop-docker:2.7.1  \
/usr/local/hadoop/py/py_runner.sh basic \
fs -ls | grep oo 
```
`OUTPUT`
```
foo	6
```


- **the words that start in "k" but end in "e"** 
*Given that our data has the following text 'quux bar foo labs quux foo foo' if we wanted to find the word 'quux' where the first letter starts with 'q' and ends with 'x' we can run the following command filter the results.* 

`INPUT`
```bash
docker run  \
-v $(pwd):/usr/local/hadoop/py \
-it sequenceiq/hadoop-docker:2.7.1 \
/usr/local/hadoop/py/py_runner.sh count \
fs -ls | grep "^q\w*x\b"
```
`OUTPUT` 
~~~
quux	4
~~~


- **the words that have a single capital letter in them?** 
*Given that our data has the following text 'quux bar foo labs quux foo foo' finding the words that contain a single capital letter would be lackluster because we dont have anything. Nonetheless below are two ways to filter words containe a single capital letter and one way to filter words with a single lowercase letter. 

`INPUT {cap}`
```
docker run  \
-v $(pwd):/usr/local/hadoop/py \
-it sequenceiq/hadoop-docker:2.7.1  \
/usr/local/hadoop/py/py_runner.sh count \
fs -ls | grep '[[:upper:]]'
```
`OUTPUT {NULL}` 
~~~

~~~


`INPUT {cap}`
```
docker run  \
-v $(pwd):/usr/local/hadoop/py  \
-it sequenceiq/hadoop-docker:2.7.1 \
/usr/local/hadoop/py/py_runner.sh count \
fs -ls | grep '[A-Z]'
```
`OUTPUT {NULL}` 
~~~

~~~

`INPUT {lowercase}`
```
docker run  \
-v $(pwd):/usr/local/hadoop/py \
-it sequenceiq/hadoop-docker:2.7.1  \
/usr/local/hadoop/py/py_runner.sh count \
fs -ls | grep '[[:lower:]]'
```
`OUTPUT` 
~~~
bar	0
foo	6
labs	0
quux	4
~~~

As you can imagine, the fix is not to hardcode all of these scenarios inside the map/reduce functions but instead, to come up with a more generic way to solve this.




























