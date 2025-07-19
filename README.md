## <span style="background-color: #FFFF00">WAREHOUSE CHALLENGE DESCRIPTION</span>

The primary goal is to maximize points by correctly identifying objects and decoding QR codes.

### <span style="background-color: #CBC3E3; font-weight:bold">SIMULATION WORLD</span>
- **Warehouse:** Contains N distinct shelves with various obstacles that the participants must avoid.
    * Navigation is completely automated; alternatively participants may utilize manual mode at their discretion.
- **QR Codes:** A QR code is placed on both sides of the shelves. It's a 30 character string that contains the following:
    1. **Shelf ID:** A natural number from 1 to n.
    2. **Heuristic:** The **angle (0-360°)** from the x-axis to the line from current to next shelf.
        * The x-axis and y-axis are given by the red and green arrow in the foxglove map respectively.
        * The angle is embedded as a 5-character string starting from the 2nd index, as a float value.
        * First heuristic (angle from x-axis to the line from robot to first shelf) will be given beforehand.
        * NOTE: Angles are measured using the center of mass of shelves and the robot in the 2-D space.
    3. **Random string**: 20 character secret code unique for each shelf used for evaluation.
    * Format: For example QR string `2_116.6_HKq3wvCg8DGyflz3oNIj8d`, shelf ID = `2` and angle = `116.6`.
- **Shelves & Curtains:** Each shelf has two rows, with 3 objects per row (6 total), visible from both front and back.
    * **All shelves, except the first one in the sequence, are completely covered by a curtain.**
    * A shelf's curtain is unveiled only when the QR of **all previous shelves** are published to `/shelf_data`.
        * For example, the curtain of shelf 3 is unveiled when QR of shelf 1 and 2 are published correctly.
    * Hence, it's most efficient to utilize the heuristic and traverse the shelves in the sequence order.

### <span style="background-color: #CBC3E3; font-weight:bold">TASK WORKFLOW (PARTICIPANT IMPLEMENTATION)</span>
Participants are responsible for implementing the logic for the following treasure hunt sequence:

1.  **Locate First Shelf:**
    * **Task:** Identify the center of mass and orientation of the first shelf.
        * By recognizing the footprint on the SLAM-generated map (`/map`, `/global_costmap`).
        * Alternatively, image processing on the feed from the buggy's front camera can be used.
        * The choice of method is at the participants' discretion.
    * ⚠️ NOTE: Exploration to unknown spaces may be required before a sufficient map is created.
2.  **Navigate to Target Shelf:**
    * **Task:** Utilize the Nav2 action client (detailed later) to move the B3RB to the shelf's vicinity.
3.  **Navigate to and Decode QR Code:**
    * **Task:** Maneuver the robot to the side of the shelf to view the QR code on the current shelf.
    * **Action:** Capture an image and implement QR decoding to extract its string.
4.  **Align for Object Recognition:**
    * **Task:** Position the buggy so its front camera captures a clear image of the objects in the shelf.
        * This facilitates accurate YOLO model recognition.
    * **Action:** Subscribe to `/shelf_objects` to receive the recognized objects from the YOLO model.
5.  **Publish Shelf Data:**
    * **Task:** Create and populate a `synapse_msgs/WarehouseShelf` message with:
        * Identified objects and count.
        * The decoded QR code string.
    * **Action:** Publish this message to `/shelf_data`.
6.  **Curtain Reveal Mechanism:**
    * **Rule:** Correctly publishing the QR for all preceding shelves in the sequence unveils the **next shelf**.
    * **Example:** Publishing the QR of shelf 1 and 2 will reveal the curtain of the 3rd shelf.
7.  **Find and Navigate to Next Shelf:**
    * **Task:** Use the decoded heuristic (angle) to determine the location of the next "treasure" spot (shelf).
        * Alternatively, participants may explore randomly to discover the remaining shelves.
    * **Action:** Explore and locate the newly revealed shelf and repeat the process from step 3.

## <span style="background-color: #FFFF00">SOLUTION SHOWCASE</span>
Code will be uploaded after competition is over
<table>
  <tr>
    <td><img src="https://github.com/syed-naqi-abbas/nxp_aim/blob/main/photo1.png" height="228" width="400"></td>
     <td><img src="https://github.com/syed-naqi-abbas/nxp_aim/blob/main/photo2.png" height="228" width="400"></td>
  </tr>
</table>
<b>Video 1 : </b><a href="https://github.com/syed-naqi-abbas/nxp_aim/blob/main/warehouse_2.mp4">https://github.com/syed-naqi-abbas/nxp_aim/blob/main/warehouse_2.mp4</a><br>
<b>Video 2 : </b><a href="https://github.com/syed-naqi-abbas/nxp_aim/blob/main/warehouse_3.mp4">https://github.com/syed-naqi-abbas/nxp_aim/blob/main/warehouse_3.mp4</a>
