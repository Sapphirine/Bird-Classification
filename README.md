# Bird-Classification
Yixiao Qu, Jiaying Yang, Qi Lv

Biodiversity is decreasing quickly recently years, plants, birds are in danger now. Bird protection needs public contribution. And bird classification and recognition via computer is key technology for it. Using technology of clustering, classification and machine learning to achieve this goal, and building internet website to make it public using available.

## Dataset: NAbirds
G. Van Horn, S. Branson, R. Farrell, S. Haber, J. Barry, P. Ipeirotis, P. Perona, and S. Belongie. Building a bird recognition app and large scale dataset with citizen scientists: The fine print in fine- grained dataset collection. In IEEE Conference on Computer Vision and Pattern Recognition (CVPR), 2015. 1, 6,


## Tensorflow:

Need to install tensorflow version 1.4.0+ to run our demo.

## Training:
------------------------
After you have created a legal excel file use the Notebook script “spit labels” to split your excel file into test.csv and train .csv. Then use generatedtfrecordsnq.py to transform images and the excel file into .record files that Tensorflow supports. Prepare configuration file like provided in our example. Produce a label map with our R script. Put these files into a folder. The arrangement should resemble the “could” folder we provided.Upload the folder into your bucket on Google Cloud. Run the following code to start training(modification is needed)
```
gcloud ml-engine jobs submit training object_detection_`date +%s` \
--job-dir=gs://nqbird/models/train2 \
--packages dist/object_detection-0.1.tar.gz,slim/dist/slim-0.1.tar.gz \
--module-name object_detection.train \
--region us-central1 \
--config /home/nq/cloud.yml \
-- \
--train_dir=gs://nqbird/models/train2 \
--pipeline_config_path=gs://nqbird/models/pets.config
```

```
gcloud ml-engine jobs submit training object_detection_eval_`date +%s` \
--job-dir=gs://nqbird/models/train \
--packages dist/object_detection-0.1.tar.gz,slim/dist/slim-0.1.tar.gz \
--module-name object_detection.eval \
--region us-central1 \
--scale-tier BASIC_GPU \
-- \
--checkpoint_dir=gs://nqbird/models/train \
--eval_dir=gs://nqbird/models/eval \
--pipeline_config_path=gs://nqbird/models/pets.config
```

After your received satisfying result , stop the training processs. Download the training checkpoints from  your Google Cloud bucket, it should be in models/train folder. Run the following code to export the checkpoints into a single model file(frozen model) that could be recognized by Tensorflow.
```
python object_detection/export_inference_graph.py \
--input_type image_tensor \
--pipeline_config_path pets2.config \
--trained_checkpoint_prefix models_train_model.ckpt-99800 \
--output_directory bird
```

ps: we didn't upload the test.record and train.record file as they are both 10G.

## website:
------------------------
- In the object detection folder, run `python config1.py`, to turn on the server, and the port is 5000, you can change the host.
- Then you can open your browser and enter `localhost:5000` or if you are running on an AWS server, you can enter your `address:5000`
- You can then choose and upload the selected picture to website and do the recognition.


