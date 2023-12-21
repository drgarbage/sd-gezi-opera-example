# Stable Diffusion Web UI Reactor API Call Example
This example demonstrates how to work with Automatic1111 using the reactor plugin via API.

## How to run
1. Install NodeJS
2. clone this repository
3. Run the following commands:

    ```
    // Install packages
    $ yarn

    // Run the debug server
    $ yarn start

    // Once the server starts, visit http://localhost:3000
    ```

4. Change the host (http://localhost:7860) to your host address.
5. Take a picture, or upload an image to start generation.
    - The camera should open by default; if it doesn't, check the security settings of your browser. For some mobile devices, camera access is only allowed when visiting with HTTPS.

## File structure
- /public/js : script library for calling sdapi.
- /public/index.html : demo of how the script library works.

## API Reference

### <a hame="SDAPI.img2img"></a>SDAPI.img2img(image, options = {})
- Parameters
    - image : full url starts with "http(s)" or a base64 image url starts with "data:image/".
    - options : json object of parameters.
    - return : an array collection of base64 image urls.
    
- Call image-to-image generation with default options:

    ```
    // import the js file

    async function main() {

        const bae64ImageURL = 'data:image/png;base64, ...';

        const HOST = 'http://localhost:7860';

        const sdapi = new SDAPI(HOST);

        const images = await sdapi.img2img(base64ImageURL);

    }
    ```

- Call image-to-image generation with custom options:

    hint: visit the /sdapi/v1/img2img section in [http://localhost:7860/docs](http://localhost:7860/docs) for more options.

    ```
    // import the js file

    async function main() {

        const bae64ImageURL = 'data:image/png;base64, ...';

        const HOST = 'http://localhost:7860';

        const sdapi = new SDAPI(HOST);

        const options = {
            prompt: '',
            negative_prompt: '',
            width: 768,
            height: 768,
            steps: 30,
            cfg_scale: 7,
            denoising_strength: 0.35,
            sampler_name: 'Euler a',
            save_images: true,
        }

        const images = await sdapi.img2img(base64ImageURL, options);

    }
    ```

- Call image-to-image with reactor enabled:

    ```
    // import the js file

    async function main() {

        // same as previous example ...

        const options = {
            // img2img options

            alwayson_scripts: {
                reactor: {
                    args: [
                        faceImage, // #0
                        true, // #1 Enable ReActor
                        '0', // #2 Comma separated face number(s) from swap-source image
                        '0', // #3 Comma separated face number(s) for target image (result)
                        "", // #4 model path
                        'CodeFormer', // #4 Restore Face: None; CodeFormer; GFPGAN
                        1, // #5 Restore visibility value
                        true, // #7 Restore face -> Upscale
                        'None', // #8 Upscaler (type 'None' if doesn't need), see full list here: http://127.0.0.1:7860/sdapi/v1/script-info -> reactor -> sec.8
                        1, // #9 Upscaler scale value
                        1, // #10 Upscaler visibility (if scale = 1)
                        false, // #11 Swap in source image
                        true, // #12 Swap in generated image
                        0, // #13 Console Log Level (0 - min, 1 - med or 2 - max)
                        0, // #14 Gender Detection (Source) (0 - No, 1 - Female Only, 2 - Male Only)
                        0, // #15 Gender Detection (Target) (0 - No, 1 - Female Only, 2 - Male Only)
                        false, // #16 Save the original image(s) made before swapping
                        0.8, // #17 CodeFormer Weight (0 = maximum effect, 1 = minimum effect), 0.5 - by default
                        false, // #18 Source Image Hash Check, True - by default
                        false, // #19 Target Image Hash Check, False - by default
                        "CUDA", // #20 CPU or CUDA (if you have it), CPU - by default
                        true, // #21 Face Mask Correction
                        0, // #22 Select Source, 0 - Image, 1 - Face Model, 2 - Source Folder
                        "", // #23 Filename of the face model (from "models/reactor/faces"), e.g. elena.safetensors, don't forger to set #22 to 1
                    ]
                }
            }
        }

        const images = await sdapi.img2img(base64ImageURL);

    }
    ```

- Call image-to-image with a specific base model (checkpoint):

    To get all available base models in current server, check out the [SDAPI.models()](#SDAPI.models).

    ```
    // import the js file

    async function main() {

        // same as previous example ...

        const options = {
            override_settings: {
                sd_model_checkpoint: '6e430eb514' // use model.hash
            }
        }

        const images = await sdapi.img2img(base64ImageURL, options);

    }
    ```


### <a hame="SDAPI.models"></a>SDAPI.models()

- List all models
    ```
    async function main() {

        const bae64ImageURL = 'data:image/png;base64, ...';

        const HOST = 'http://localhost:7860';

        const sdapi = new SDAPI(HOST);

        const models = await sdapi.models();

        console.log(models[0].title); // print model name
        console.log(models[0].hash); // print model hash
    }
    ```