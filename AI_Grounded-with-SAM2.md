상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# Grounded with SAM2
- We use **Grounded SAM2** to automatically detect and segment objects in an image based on a text prompt (e.g., `"monitor. keyboard. mouse."`).
- It combines **Grounding DINO** (for text-based object detection) with **SAM2** (for high-quality segmentation), producing pixel-accurate masks for each object.

## Masks are used
- visualize object boundaries
- generate per-class binary masks
- or apply **inpainting models** to replace or modify specific regions

## Example Output

### Test1

- Origin Image
  ![test](https://github.com/user-attachments/assets/c66db3fb-6b8a-444b-bce8-ac9f05d2e363)

- Grounded SAM2.1
  ![Unknown-3](https://github.com/user-attachments/assets/bf6f3cb5-e09b-4068-ac3a-bd14d1e38eea)

- Mask

|desk|deskmat|laptop|monitor|mouse|
|--|--|--|--|--|
|![desk](https://github.com/user-attachments/assets/ff431213-0c7d-40e1-8dbb-5ec70d835e0e)|![deskmat](https://github.com/user-attachments/assets/567ce656-c769-45e8-b515-676e83bf40e4)|![laptop](https://github.com/user-attachments/assets/3aa4b9a1-21c1-4945-8651-16ce9844c510)|![monitor_desktop](https://github.com/user-attachments/assets/f64b08b4-46c0-449a-b183-5db887dbc92c)|![mouse](https://github.com/user-attachments/assets/cdd2f9c1-25f0-482d-ab3b-48f75316d96d)|

### Test2

- Origin Image
  ![test](https://github.com/user-attachments/assets/15047cc2-1674-4bc9-bd01-77fa5612ed65)

- Grounded SAM2.1
  ![bounding box with mask](https://github.com/user-attachments/assets/83de6879-332b-4cf1-a31e-88d4fbef3622)

- Mask

|desk|deskmat|monitor|
|--|--|--|
|![desk](https://github.com/user-attachments/assets/41814e39-ce79-4d47-aa71-ab90e6f70935)|![deskmat](https://github.com/user-attachments/assets/a6a88a41-3641-4315-81c2-822e863d8d84)|![monitor_desktop](https://github.com/user-attachments/assets/14cbbc80-8439-4e4e-bdee-32b8bfb24e78)|

|keyboard|mouse|speaker|
|--|--|--|
|![keyboard](https://github.com/user-attachments/assets/71cbaef0-9da7-4e48-9d53-50fc2d110f68)|![mouse](https://github.com/user-attachments/assets/7f285cda-9846-4c55-a96d-730a2bf957b2)|![speaker_speaker](https://github.com/user-attachments/assets/0bc06174-0bb6-4ce7-bdb5-3a3aff3f9a9c)|

### Test3

- Origin Image
  ![test3](https://github.com/user-attachments/assets/32fca0f8-f289-4342-b70f-5ba59c80fc80)

- Grounded SAM2.1
  ![Unknown-3](https://github.com/user-attachments/assets/9bc6f635-e280-411f-9d21-725ae8bdde36)

- Mask
  
|desk|deskmat|monitor|
|--|--|--|
|![desk](https://github.com/user-attachments/assets/0db36cf0-14ef-47a0-9600-6870328e4a38)|![deskmat](https://github.com/user-attachments/assets/df08dd96-1f63-4236-95f0-05e34bdda23a)|![monitor_desktop](https://github.com/user-attachments/assets/f8cbfbc6-b05b-4693-a84e-6fe7affb348a)|


|laptop|keyboard|mouse|speaker|
|--|--|--|--|
|![laptop](https://github.com/user-attachments/assets/fd6c5cb9-1f2f-45fa-ae56-47c6edfbfcf2)|![keyboard](https://github.com/user-attachments/assets/bd23bbb4-88d5-46e0-8f0b-8c476e11fe4b)|![image](https://github.com/user-attachments/assets/65732eb6-e856-4f11-81d7-9297f3b194c8)|![speaker_speaker](https://github.com/user-attachments/assets/95e61e5a-bff7-4021-90d8-b0e9b2b71a0e)|

### Test4

- Origin Image
  ![test4](https://github.com/user-attachments/assets/444672c6-4cfb-48a0-99a0-3e250ee1517e)

- Grounded SAM2.1
  ![Unknown-4](https://github.com/user-attachments/assets/0e0bfad8-c37e-4aaf-9022-0f97ce65ec26)

- Mask

|desk|monitor|desktop|
|--|--|--|
|![desk](https://github.com/user-attachments/assets/b84eaab9-7a71-488c-a55e-f2918ded8de0)|![monitor_desktop](https://github.com/user-attachments/assets/dba02701-95cd-4884-a67e-6d6491fbc40b)|![laptop_desktop](https://github.com/user-attachments/assets/5c8f3992-36b5-4afa-9082-dcba745a3faa)|

|keyboard|mouse|speaker|
|--|--|--|
|![keyboard](https://github.com/user-attachments/assets/41a8eb20-b9be-44d7-b1f2-777b29f98f7a)|![mouse](https://github.com/user-attachments/assets/4557e555-9436-4118-917e-ae252ebd0df2)|![speaker_speaker](https://github.com/user-attachments/assets/04181731-a2bb-46bc-b2e4-67f72a835302)|


## Issues
- When saving masks, objects with the same class name overwrite each other. Only the last instance is preserved
- In some cases (e.g., test1), unintended objects such as the other person's mouse may be detected
- If the label confidence is low or ambiguous, a single object may be split into multiple segments (e.g., `"desktop_monitor"` detected as two parts)

## Next Steps
- Merge all masks into a single combined mask
- Apply SDXL inpainting using the original image and the merged mask
- Fine-tune the SDXL inpainting model for better domain-specific results

# Reference
- [IDEA-Research / Grounded-SAM-2](https://github.com/IDEA-Research/Grounded-SAM-2)
