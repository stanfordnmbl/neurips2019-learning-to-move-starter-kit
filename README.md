![CrowdAI-Logo](https://github.com/crowdAI/crowdai/raw/master/app/assets/images/misc/crowdai-logo-smile.svg?sanitize=true)
# NIPS 2018 : AI for Prosthetics Challenge Round2 Starter Kit
[![gitter-badge](https://badges.gitter.im/crowdAI/NIPS-Learning-To-Run-Challenge.png)](https://gitter.im/crowdAI/NIPS-Learning-To-Run-Challenge)   

Instructions to make submissions to the final round of the [NIPS 2018 : AI for  Prosthetics Challenge](https://www.crowdai.org/challenges/nips-2018-ai-for-prosthetics-challenge).

Participants will have to submit their code, with packaging specifications, and the evaluator will automatically build a docker image and execute their agent against the `ProstheticsEnv` with `difficulty=2` and a series of random seeds for 10 episodes.

### Setup
* **docker** : By following the instructions [here](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
* **nvidia-docker** : By following the instructions [here](https://github.com/nvidia/nvidia-docker/wiki/Installation-(version-2.0))
* **crowdai-repo2docker**
```sh
pip install crowdai-repo2docker
```
* **Anaconda** (By following instructions [here](https://www.anaconda.com/download)) At least version `4.5.11` is required to correctly populate `environment.yml`.
* **osim-rl** (By following instructions [here](http://github.com/stanfordnmbl/osim-rl/))
**IMPORTANT** : Please note that you will need `osim-rl` version `>=2.1.5` to be able to submit which you can update in your anaconda env by :
```
pip install -U osim-rl
```
* **Your code specific dependencies**
```sh
# If say you want to install PyTorch
conda install pytorch torchvision -c pytorch
```

### Clone repository 
```
git clone git@github.com:crowdAI/nips2018-ai-for-prosthetics-round2-starter-kit.git
cd nips2018-ai-for-prosthetics-round2-starter-kit
```

### Test Submission Locally
```
cd nips2018-ai-for-prosthetics-round2-starter-kit
export IMAGE_NAME="ai-for-prosthetics-agent"

# Build docker image for your submission
./build.sh

# In a separate tab : run redis server 
./run_redis.sh 

# In a separate tab : run local grader 
./run_local_grader.sh

# In a separate tab :
# Finally, run your agent locally by :
./debug.sh
```

# How do I specify my software runtime ?
The software runtime is specified by exporting your `conda` env to the root 
of your repository by doing : 
```
conda env export --no-build > environment.yml
```
This `environment.yml` file will be used to recreate the `conda environment` inside the Docker container.
This repository includes an example `environment.yml`

# What should my code structure be like ?

Please follow the structure documented in the included [run.py](https://github.com/crowdAI/nips2018-ai-for-prosthetics-round2-starter-kit/blob/master/run.py) to adapt 
your already existing code to the required structure for this round.

## Important Concepts

### Repository Structure
* `crowdai.json`
  Each repository should have a `crowdai.json` with the following content : 
```json
{
  "challenge_id" : "crowdai-marLo-2018",
  "grader_id" : "crowdai-marLo-2018",
  "authors" : ["your-crowdai-username"],
  "description" : "sample description about your awesome agent",
  "license" : "MIT",
  "gpu":false
}
```
This is used to map your submission to the said challenge, so please remember to use the correct `challenge_id` and `grader_id` as specified above.

Please specify if your code will a GPU or not for the evaluation of your model. If you specify `true` for the GPU, a **NVIDIA Tesla K80 GPU** will be provided and used for the evaluation.

### Packaging of your software environment
You can specify your software environment by using all the [available configuration options of repo2docker](https://repo2docker.readthedocs.io/en/latest/config_files.html). (But please remember to use [crowdai-repo2docker](https://pypi.org/project/crowdai-repo2docker/) to have GPU support)   

The recommended way is to use Anaconda configuration files using **environment.yml** files.

```sh 
# The included environment.yml is generated by the command below, and you do not need to run it again 
# if you did not add any custom dependencies

conda env export --no-build > environment.yml

# Note the `--no-build` flag, which is important if you want your anaconda env to be replicable across all 
```

### Debugging the packaged software environment

If you have issues with your submission because of your software environment and dependencies, you can debug them, by first building the docker image, and then getting a shell inside the image by : 
```
nvidia-docker run --net=host -it $IMAGE_NAME /bin/bash 
```
and then exploring to find the cause of the issue.

### Code Entrypoint
The evaluator will use `/home/crowdai/run.sh` as the entrypoint, so please remember to have a `run.sh` at the root, which can instantitate any necessary environment variables, and also start executing your actual code. This repository includes a sample `run.sh` file.
If you are using a Dockerfile to specify your software environment, please remember to create a `crowdai` user, and place the entrypoint code at `run.sh`.

## Submission 
To make a submission, you will have to create a private repository on [https://gitlab.crowdai.org](https://gitlab.crowdai.org).

You will have to add your SSH Keys to your GitLab account by following the instructions [here](https://docs.gitlab.com/ee/gitlab-basics/create-your-ssh-keys.html).
If you do not have SSH Keys, you will first need to [generate one](https://docs.gitlab.com/ee/ssh/README.html#generating-a-new-ssh-key-pair).

Then you can create a submission by making a *tag push* to your repository on [https://gitlab.crowdai.org](https://gitlab.crowdai.org). **Any tag push to your private repository is considered as a submission**   
Then you can add the correct git remote, and finally submit by doing : 

```
cd nips2018-ai-for-prosthetics-round2-starter-kit
# Add crowdAI git remote endpoint
git remote add crowdai git@gitlab.crowdai.org:<YOUR_CROWDAI_USER_NAME>/nips2018-ai-for-prosthetics-round2-starter-kit.git
git push crowdai master

# Create a tag for your submission and push
git tag -am "v0.1" v0.1
git push crowdai master
git push crowdai v0.1

# Note : If the contents of your repository (latest commit hash) does not change, 
# then pushing a new tag will **not** trigger a new evaluation.
```
You now should be able to see the details of your submission at : 
[gitlab.crowdai.org/<YOUR_CROWDAI_USER_NAME>/nips2018-ai-for-prosthetics-round2-starter-kit/issues](gitlab.crowdai.org/<YOUR_CROWDAI_USER_NAME>/nips2018-ai-for-prosthetics-round2-starter-kit/issues)

**NOTE**: Remember to update your username in the link above :wink:

In the link above, you should start seeing something like this take shape (each of the steps can take a bit of time, so please be patient too :wink: ) : 
![](https://i.imgur.com/Kc7M8zH.png)

and if everything works out correctly, then you should be able to see the final scores like this : 
![](https://i.imgur.com/9RT2jFi.png)

**Best of Luck** :tada: :tada:


# Author 
Sharada Mohanty <https://twitter.com/MeMohanty>
