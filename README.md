libcircle 0.0.1
===============

NOTE: This file is not a complete API and is subject to rapidly change. Please don't develop any software against it yet. Thanks!

libcircle is an API for distributing embarrassingly parallel workloads using self-stabilization. Local actions work towards a global objective in a finite number of operations. It is not meant for applications where cross-process communication is necessary. The core algorithm used is based on Dijkstra's 1974 token ring proposal.

```C
void create_some_work(CIRCLE_handle *handle)
{
    /*
     * This is where you should generate work that needs to be processed.
     * For example, if your goal is to lstat files on a large filesystem,
     * this is where you would readdir() and and enqueue directory names.
     *
     * This should be a small amount of work. For example, only enqueue the
     * filenames from a single directory.
     */
    handle.enqueue((void *)my_data);
}

void process_some_work(CIRCLE_handle *handle)
{
    /*
     * This is where work should be processed. For example, this is where you
     * should lstat the files in the directories that were added by your
     * create_some_work callback.
     */
    (char *)my_data = handle.dequeue();
    ...
    finished_work = lstat(my_data, ...);
    ...
    store_in_database(finished_work);
}

/*
 * This is the handle that holds everything that libcircle needs, such as
 * a reference to the queue data structures.
 */
CIRCLE_handle *handle;
handle = CIRCLE_handle_create();

/*
 * Processing and creating work are done through callbacks.
 * This is where one sets a callback function to create work for libcircle.
 */
void (*create_some_work)(CIRCLE_handle *handle);
handle.create_work(&create_some_work);

/*
 * Processing and creating work are done through callbacks. 
 * This is where one sets a callback function to create work for libcircle.
 */
void (*create_some_work)(CIRCLE_handle *handle);
handle.define_create_work(&create_some_work);

/*
 * To tell libcircle which function to use to process work, add your callback here.
 */
void (*process_some_work)(CIRCLE_handle *handle);
handle.define_process_work(&process_some_work);

/*
 * Always free the libcircle context with the function provided.
 *
CIRCLE_handle_free(c);
```

## EOF ##
