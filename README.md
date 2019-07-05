# GRG_Subway_Check_in_System
This is description for subway check-in system progress

# How to run?
1. Copy `subway.tar.gz` to "Running machine"
2. extract here: `tar -xzf subway.tar.gz` or using mouse to extract
3. Run: `bash grg-subway.sh` to run program, requring password: __system signin password__

# What's New?

2019-07-05: First time submit program
    - Support 1 to 4 webcam running in one frame
    - Support change webcam position
    - support rotate webcam 180 degree
    - support send & receive data through serial port
    - support change serial port parametter
    
    


# How to build?
This part for deverloper only:
All things to do: `bash 2-run-build.sh`

Contents:
1. Run normally: `python subway.py` to ensure every things ok
2. Run `pyinstaller subway.py` to build
3. Copy folder `data` paste inside folder `dist/subway`
    results:
    
    ```
    ~/subway/data/
                  ├── labels
                  ├── obj
                  ├── obj.data
                  ├── obj.names
                  ├── yolov3-tiny-videos.cfg
                  └── yolov3-tiny-videos.weights

    ```
4. Create running script: `grg-subway.sh`
5. Compress `subway` folder, copy to running machine
