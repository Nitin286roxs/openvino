# Base Image Creation
- Caffe Installation

    Setup Caffe in ```openvino/ubuntu18_runtime:latest```  for the siamese model.
 ```sh
 apt update && apt install caffe-cpu
 ```
 - Caffe Installation Verification
 
    After installation verify that is ```import Caffe``` working or not.
    
    If you are getting ```ImportError: cannot import name '_validate_lengths'``` error, degrade ```numpy==1.15.0``` using this command 
    
    ```pip3 install numpy==1.15.0```
    
    With this change, import caffe should work. If not refer [here](https://www.programmersought.com/article/63651271558/)

# Model Optimizer


   Use ```openvino/ubuntu18_dev:latest```  for model conversion.
   
- Converting Your Caffe* Model

    ```bash
    cd /opt/intel/openvino_2021.3.394/deployment_tools/model_optimizer/
    python3 mo_caffe.py --input_model /app/helmet_1/siamese_training_combinedhelmet_128x128__iter_200000.caffemodel 
                        --input_proto /app/helmet_1/deploy_helmet_with_dist.prototxt
                        --input_shape [1,3,128,128]
                        --output_dir /app/
     ```

# Cutting parts of the model caffe model
   model is too complex (contains lots of unsupported operations that cannot be easily implemented as custom layers), so the complete model cannot be converted in one shot.
   
   single custom layer or a combination of custom layers is isolated for debugging purposes.

 Model Optimizer provides command line options --input and --output to specify new entry and exit nodes, while ignoring the rest of the model:
- --input option accepts a comma-separated list of layer names of the input model that should be treated as new entry points to the model.
- --output option accepts a comma-separated list of layer names of the input model that should be treated as new exit points from the model.


    ```bash
    python3 mo_caffe.py --input_model /app/helmet_1/siamese_training_combinedhelmet_128x128__iter_200000.caffemodel
                        --input_proto /app/helmet_1/deploy_helmet_with_dist.prototxt
                         --input_shape [1,3,128,128]
                         --output_dir /app/
                         --output 'pool5/7x7_s1_right','dist'
    ```
    

