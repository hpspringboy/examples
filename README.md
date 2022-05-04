#Template
Motivation:
Kubernetes itself does not provide any templating mechanism.  There are multiple benefits of a template design for the Kubeflow community.  The first is to expedite the development of a AI modeling process where the switch of an intermedium model parameters are frequent.  One example is the switch of parameters of  hidden layers in the transfer learning process.  The second is a fast synthesis of a new AI training flow and exploration of the optimization process.  We propose a template construct for hidden layers replacement during transfer learning process.  The deployment medium is a static yaml file ready for kubeflow pipeline execution. 
Methods:
We define the template workflow as follows: execute and store the state of an AI flow, treats the workflow as an active object.  The template contains a list entry points for a functions that define the instructions to execute.  An entry point specifies the main function of the workflow or the first template to be executed.
To fulfill the definition of Kubernetes CRD custom resources, we use Kubernetes container for the construct.  We wrap the input layer of the model through an empty container, and provide it to other models for operation, and finally use the Yaml file to provide parameter replacement, artifacts, fixtures , loop and recursion workflows.  The following are the steps for our transfer learning template construct.
1.	trainOp serves as the main entry point, configure the target model and save the path to the model files.  When faced with multiple Kubernetes clusters, we provide resources that can be applied to all environments with minimal modifications.  When the application is running in the staging environment, it should connect to the staging database and use environment variables to inject the correct database URL.
![image]
2.	The following is the terminology explanation of the above preparation operation.
(a)model_relative_path - to save the model, save a model with tensorflow.keras to a folder under the current directory of model_path.
(b).model_name - to use the generated model name as the file holder for all generated model files.
(c).model_dir = os.path.join(output_model_dir, model_name) save the model to the path.
(d).pathlib is an object-oriented file system path, which encapsulates the operations related to files and folders in categories such as Path, making the operations of files/folders more object-oriented. Classes are provided that represent filesystem paths, have semantics applicable to different operating systems, and also provide I/O operations.
3.	pathlib.Path(model_dir).mkdir(parents=True, exist_ok=True)—Write the transfer learning input layer into the empty folder:
![image]
4.	Write the required environment and parameters into requirements.txt:
![image]
5.	Build a pipeline components, and a pod to store the model:
![image]
Add a comment to the defined templated_pipeline_func category to configure the model_relative_path and model_name into the workflow, as well as the default resources for cpu and memory.  Finally, kfp.compiler encodes the model into a yaml file and outputs it as a compressed file, and runs it in kubeflow as follows:
 
6.	The packaged model input layers can be seen in the pod:
![image]
7.	Use kubectl in the Minikube to check for the log of the pod from 6.
![image]
8.	The template workflow is summarized as below:
![image]

