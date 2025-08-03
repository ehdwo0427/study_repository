상위 문서로 이동 : [AI Wiki](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI-Wiki)

# AI_SDXL_inpainting_with_GroundedSAM2

- The SDXL Inpainting model edits or fills parts of an image based on a mask and a text prompt.
<br/>
<br/>

## Model Test

- Origin Image
  ![test3](https://github.com/user-attachments/assets/705fe50d-003e-44de-ad7d-095e1e2de2b0)

- Mask Image
  ![inpainting_background_mask](https://github.com/user-attachments/assets/50f4f5fd-378c-4db7-bc60-dca159e26db4)
  We used Grounded SAM2 for masking to precisely define the areas for SDXL Inpainting. [Grouded with SAM2](https://github.com/100-hours-a-week/16-Hot6-wiki/wiki/AI_Grounded-with-SAM2)

### Example Output

|1|2|3|
|--|--|--|
|![inpainted_result-2](https://github.com/user-attachments/assets/e596db74-48e2-420b-afc4-32eedb23d2f0)|![inpainted_result-3](https://github.com/user-attachments/assets/c3d78155-7c7b-49a9-b181-cb46a13d0cf3)|![inpainted_result-4](https://github.com/user-attachments/assets/4d77a020-d333-41f3-acdc-748065e63dd7)|

- These images were generated using different prompts to explore various outputs.

## Issue
- The mask images are a bit jagged, which results in less clean inpainting outputs.
- Imprecise masking of the 'desk' and 'deskmat' appears to cause issues in the final output.

## Next Steps
- Generate a mask image that excludes the desk and deskmat from the inpainting area.

## Reference
[diffusers/stable-diffusion-xl-1.0-inpainting-0.1](https://huggingface.co/diffusers/stable-diffusion-xl-1.0-inpainting-0.1)
