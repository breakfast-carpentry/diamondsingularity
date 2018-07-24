# Singularity Example

## Instructions to create the Singularity image and run the container!

1. Pull this Github repo
On a Jetstream instance,
```
git clone https://github.com/breakfast-carpentry/diamondsingularity.git
```
Go into the repo,
```
cd diamondsingularity
```

2. Build the Singularity image
```
sudo singularity build diamond.simg Singularity
```

3. Run the python script with the container.
```
singularity run diamond.simg
```

You can also go into the container and play with it interactively
```
sudo singularity shell diamond.simg
```

You can exit out of the shell by typing `exit` inside the container
```
Singularity diamond.simg:~> exit
```

## Overview of what we did morning 7/19/18
This morning our goal was to build and run a Singularity image with our own script and data and get output from our code.

- We first used Jetstream (because we have root access there).
- We started with our diamond directory from yesterday - in my case `/home/aglad/diamond_blast`.
- I wrote a toy python script, `script.py`, which just prints "hello":
```
print('Hello')
```
- We then started to write our Singularity recipe. We started with only an empty ubuntu image just to check that we could create that simple image correctly. (to write the Singularity recipe I used a Jupyter text editor so I wouldn't need to use a command line editor)
```
Bootstrap: docker
From: ubuntu
```
- We built the Singularity image and called it diamond.simg.
```
sudo singularity build diamond.simg Singularity
```
- We ran the diamond.simg container interactively so that we could test our setup
```
sudo singularity shell diamond.simg
```
- After we installed python and checked that it worked, we copied those commands into our Singularity recipe:
```
Bootstrap: docker
From: ubuntu

%post
    apt-get update
    apt-get install -y python3
```
- Finally we ran our python script with our Singularity container
```
singularity exec diamond.simg python3 script.py
```

## Overview of what we did morning 7/24/18

This morning our goal was to run a Singularity container with multiple apps with input data and output data.

- We started on Jetstream.
- We cloned the repo following the instructions above.
- We edited the Singularity recipe:
```
Bootstrap: docker
From: ubuntu

%post
    apt-get update
    apt-get install -y python3
    echo "I'm in post"

%files
   script.py /tmp

%runscript
   echo "Containers are cool"
   python3 /tmp/script.py
```

- We explored the difference between `singularity run` and `singularity exec`.
    If we run,
    ```
    singularity run diamond.simg
    ```
    The Singularity container runs the runscript from the recipe and we see,
    ```
    Containers are cool
    hello
    ```
    
    On the other hand, if we run,
    ```
    singularity exec diamond.simg
    ```
    The Singularity container runs the command after `diamond.simg` (which in the above case is nothing), so we get an error,
    ```
    ERROR  : No program name to exec
    ABORT  : Retval = 255
    ```
    We can run,
    ```
    singularity exec diamond.simg python3 script.py
    ```
    This gives,
    ```
    hello
    ```
    Or we can run,
    ```
    singularity exec diamond.simg echo "I am echoing"
    ```
    This gives,
    ```
    I am echoing
    ```
        
- We shared Singularity containers with other people.

- We ran apps.
    - In order to run multiple things in one container we can use apps.
    - We created a new script, `otherscript.py`,
    ```
    print('Goodbye')
    ```
    - We added the app other to the Singularity recipe
    ```
    %apprun other
       python3 /tmp/otherscript.py
    ```
    - Then, we built the Singularity container
    ```
    sudo singularity build diamond.simg Singularity
    ```
    - If we `run` or `exec` as above, we will still get the same outputs.
    - To check what apps we included in the Singularity container,
    ```
    singularity apps diamond.simg
    ```
    This lists our app,
    ```
    other
    ```
    - We can run our app,
    ```
    singularity run --app other diamond.simg
    ```
    This gives,
    ```
    Goodbye
    ```
    - We could also have multiple apps that do different things.



### Homework

1. Create a new app in the Singularity file called `download` that downloads the following csv file to the `/tmp/` directory: <https://gist.githubusercontent.com/julianpistorius/e380eee2743a59e8aad2cb66e307736b/raw/216071913bc377d2d2153e4b4c6bf1770320f5a7/real_data.csv>

2. Create a new app called `total_lines` that prints the total number of lines in the above file. For example:
    ```bash
    singularity run --app total_lines diamond.simg
    7
    ```
3. Create a new app called `filter` that prints out the lines that contain a string (passed as a parameter). For example:
    ```bash
    singularity run --app filter diamond.simg piano
    Samantha,piano,Kit
    Mark,piano,Cranky
    ```
4. Create a new app called `notebook` that starts a Jupyter notebook. For example:
    ```bash
    singularity run notebook
    ``` 
