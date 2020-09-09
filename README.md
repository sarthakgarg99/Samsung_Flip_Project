# Samsung Flip - Accidental Erasing Problem


The Flip is an easy to use device which feels just like a writing board and it uses a passive pen technology which means that you can easily write using any passive object pen , marker , etc. It also detects the thickness of the object and on that basis it works in 3 modes pen , highlighter and eraser. It also allows simultaneous writing and erasing.

## Problem Statement
There is the functionality of erasing using the palm. This functionality is provided as it make erasing stuff easy and the user feels like he/she is working on a real writing board. Moreover, the Flip works with any passive stylus like a pen or stick and doesnt require a electronic stylus. But the problem with this functionality is that sometimes when the user doesn’t really want to erase but due to unintended palm touches on the screen while writing, the devices senses it as an erase event and erases the part written on the board. 

## Aim
The aim of this project is to make the device capable of differentiating between intended and
unintended touches so that the user gets a better experience. This is commonly referred to as accidental palm rejection problem.

## Dataset Generation
We visited Samsung R&D Institute, Delhi and collaborated with their engineers to generate datasets for intentional erase and unintentional erase while writing and not writing. The challenge was that we needed a lot of data and the datase generation was done manually so it took a lot of effort.


## Approach
1. In the Flip, there are events for different kinds of cases. When you place a pen or eraser on the display, it is a pen down event and various values such as x coordinate, y coordinate and radii are given. If the pen isn't lifted, then it is in writing operation and during this time, there is no information about radii (size). If there are multiple events, then they are assigned different event IDs.
2. We looked at several research papers in this field (can be found in the Research Papers folder) . 
3. The first approach that we tried out was that of approximating the various positions of the palm as squares and the pen tip as another squares. Then, on the basis the position, size and overlap of these squares, classify touches as pen, and erase int(ended and unintended).
3.1 This approach did not work very well because the touch input coordinates that the flip takes are average values over an area and so the size and position of squares did not reflect the actual movement of the palm.
4. The next approach we tried was to apply Machine Learning and to make a model
which will consist of the following features:
4.1 Relative velocity of eraser with respect to pen
4.2  Size of the eraser
4.3 Shape of the patch
4.4 Distance of the palm with respect to the pen

5.1. The problem with the above approach was that we had to reject the eraser as soon as the eraser down event is detected and so we won’t really be able to get the relative velocity of the eraser wrt to pen as our code need to classify the eraser as intended or unintended as soon as it’s down event is triggered.

5.2. Another problem that we faced was that there was no way of classifying the shape of the patch as the device only gave radius_x radius_y and radius as the output so we couldn't use that feature either.
5.3 But this problem was solved by modifying the feature set and just seeing those dataset values where the event changes from pen up to pen down. This reduced the size of dataset considerably and so the decision tree model did not give very accurate predictions. Moreover, we realised that machine learning based solutions were causing a noticeable lag between the user touch and screen response.


7. Finaly, We made a python script that extracts parameters including :
6.1 distance of erase pen down event from the just previous pen writing event.
6.2 Whether the pen writing event has a y coordinate greater than the erase pen down event ( this is an assumption that hand touching would happen below the pen touch, which is generally the case)
6.3 The radii values of the erase pen down event .
7. We used all the information extracted and applied a GMM (Gaussian Mixture Model) to learn the boundaries between indended and accidental touch. 
8. If the bounds are satisfied, then it is accidental erase and that entire erase pen down event is rejected. So even if there is subsequent accidental erasing after that, it will be rejected. This  improves the performance.

## Results
The results that we got from Samsung after testing our approach classify and reject most of the common cases and worked well, thus improving the user experience. 
