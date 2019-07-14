# GRG_Subway_Check_in_System
This is description for subway check-in system progress
go to [here](https://github.com/tuananhvip/GRG_Subway_Check_in_System) for newest detail

# 1. Data transfer detail
When send control command to serial port of running machine, system will send data to embedded system. Here is commands

    . Start receiving data: `Start;`
    . Stop receiving data:  `Stop;`
    . Data structure: :I:N:ID0,x0,y0:ID1,x1,y1: ... :ID(n-1),x(n-1),y(n-1);
        where:
            - I: index of data frame
            - N: total number of detected people
            - IDi: ID tracking person i-th
            - xi,yi: center point position of detected people, i=0,..n-1
        note: in data frame, contain space for easy debug.
    examples:       
        
        
# 2. How to run?
## prepare program
1. Copy `subway.tar.gz` to "Running machine"
2. extract here: `tar -xzf subway.tar.gz` or using mouse to extract

## Run program
Run: `bash grg-subway.sh` to run program, requring password: __system signin password__

# 3. What's New?
## 2019-07-14: Second time update
### Feature
    - Add tracking people feature, now each frame, people in one half right area will be track (go from right to left)
    - People pass: it will create 'pass' person when a person go through the center of the doorway
    - Change training mathod: load previous training weight to continues training.
    


## 2019-07-05: First time submit program
### Feature

    - Support 1 to 4 webcam running in one frame
    - Support change webcam position
    - support rotate webcam 180 degree
    - support send & receive data through serial port
    - support change serial port parametter
    
### Data transfer detail
When send control command to serial port of running machine, system will send data to embedded system. Here is commands

    . Start receiving data: `Start;`
    . Stop receiving data:  `Stop;`
    . Data structure: `:I:N:x0,y0:x1,y1: ... :x(n-1),y(n-1);`
        where:
            - I: index of data frame
            - N: total number of detected people
            - xi,yi: center point position of detected people, i=0,..n-1
        note: in data frame, contain space for easy debug.
        
      examples:       
        :192:5: 114, 366: 116, 120: 387, 353: 546, 153: 269, 113;\n
        :193:6: 116, 122: 114, 366: 383, 353: 546, 153: 263, 112: 529, 379;\n
        :194:5: 116, 122: 110, 368: 383, 353: 546, 151: 265, 112;\n
        :195:4: 117, 124: 110, 367: 383, 353: 545, 151;\n
        :196:5: 116, 125: 110, 369: 379, 353: 532, 374: 252, 113;\n
        :197:5: 115, 127: 110, 369: 379, 353: 532, 374: 241, 113;\n
        :198:6: 115, 127: 108, 371: 368, 350: 546, 136: 241, 113: 549, 371;\n
        :199:6: 115, 127: 108, 371: 368, 350: 546, 136: 241, 113: 549, 371;\n
        :200:6: 114, 130: 546, 135: 105, 374: 362, 350: 549, 370: 237, 113;\n
        :201:6: 113, 133: 546, 135: 362, 350: 105, 374: 549, 370: 232, 112;\n
        :202:7: 113, 133: 551, 369: 546, 132:  90, 378: 358, 354: 232, 112: 186, 360;\n    



# 4. How to build?
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
