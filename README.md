[![HitCount](http://hits.dwyl.io/hastagAB/GSoC-19.svg)](http://hits.dwyl.io/hastagAB/GSoC-19)
![Atarashi](https://img.shields.io/pypi/status/atarashi)
![License](https://img.shields.io/pypi/l/atarashi)
![GitHub stars](https://img.shields.io/github/stars/fossology/atarashi?style=social)




                               _______       _____             _____ _    _ _____ 
                            /\|__   __|/\   |  __ \     /\    / ____| |  | |_   _|
                           /  \  | |  /  \  | |__) |   /  \  | (___ | |__| | | |  
                          / /\ \ | | / /\ \ |  _  /   / /\ \  \___ \|  __  | | |  
                         / ____ \| |/ ____ \| | \ \  / ____ \ ____) | |  | |_| |_ 
                        /_/    \_\_/_/    \_\_|  \_\/_/    \_\_____/|_|  |_|_____|
                                                                  
                                                           

# SUMMARY

Atarashi scans for license statements in open source software, focusing on text statistics and information retrieval algorithms. It was designed to work stand-alone and with FOSSology Software. 
Earlier, it worked only on the simple command-line interface. for it's better usage it needed to be integrated with the fossology software.

My project was to integrate Atarashi with the fossology software and to create a stable GUI that uses various existing fossology features. The integration part was done using Ninka, which was used to wrap atarashi around fossology.

To improve atarashi and continue its development, I had to make a pip package of atarashi and publish it to PyPI. Along with that, I had to establish a machine learning model and implement a new algorithm using it. The new algorithm **Semantic Text Similarity** works on the [Gensim](https://radimrehurek.com/gensim/models/doc2vec.html) implementation of [Doc2Vec](https://cs.stanford.edu/~quocle/paragraph_vector.pdf) and finds the most similar docs using the cosine similarities of vectors. With the gradual improvement in the future will make this model more accurate and atarashi more powerful and faster than ever. 

Also, I had to create an **Evaluation Script** for the existing and upcoming algorithms for atarashi to validate its accuracy and reliability. Evaluation is based on time and the accuracy of the agents. This will be beneficial to get the best open-source license scanning algorithms.

# CONTRIBUTIONS 
## 1. Package Atarashi and Publish to PyPI
To integrate Atarashi with fossology we had to make a package of it and publish it to PyPI so that we can pip install atarashi in the fossology system and use that in the software. To make a publishable python package there needs to be certain organized directory and file system. The structure of Atarashi was already organized and setup.py had every bit of detailed which was needed to publish. This saved me a lot of time and work.

There were some minor errors like the classifiers in setup.py metadata was a little bit different than actually standardized classifiers provided by PyPI. Changed  ``Development Status ::  Pre-Alpha" to “Development Status :: 2 - Pre-Alpha”`` and
``"License :: OSI Approved :: GPL v2.0 License" to “License :: OSI Approved :: GNU General Public License v2 (GPLv2)``. 
One more error occurred while publishing when it was not accepting the CodeComment as it was external so I had to remove it from setup.py. 

Currently, the Package is live at: https://test.pypi.org/project/atarashi/ 

Atarashi can be installed in the system using `pip install -i https://test.pypi.org/simple/ atarashi`

## 2. Integrating Atarashi to Fossology
To integrate Atarashi with fossology Ninka Agent was to be modified and used as a wrapper. The codebase was to be modified accordingly with respect to atarashi. 
### The Modifications are as follows: 
* Changed various Classes and Functions accordingly. 
* Added the command of atarashi agent to run on bash. 
* Parsed the output format of Atarashi i.e. JSON to the result output format of Ninka. for that, I used Jsoncpp library that was already used there is FOSSology before.
* Modified the makefiles accordingly
* Compiled atarashiWrapper codebase and connected to Fossology database.
* Added atarashiWrapper to fossology Scheduler.

The working UI needs to be fixed which was done by [@GMishx](https://github.com/GMishx)
* Cherry Picked the commit [GMishx@3b31c17](https://github.com/GMishx/fossology/commit/3b31c17996691c5b3beebe90506876efb5f361dc) and added to the PR for working UI
* Added the mod_deps files for Python, pip and atarashi Installation.

The Pull Request for the changes is : [feat(atarashi): Add Atarashi to FOSSology #1408](https://github.com/fossology/fossology/pull/1408)

The UI is working fine and the agent can be scheduled for `upload < for file < Select optional analysis`

![atarashiWrapper](/files/atarashi-UI.png)

## 3. Algorithms Evaluator
Before creating any new algorithm for Atarashi, it was suggested that a script should be there to evaluate the algorithms to get an accurate and reliable license scanner agent. Also it will be easier to get the best scanner agent out of all. 

The evaluation is done on the basis of two factors:
1. Time taken by the agent
2. Accuracy of the result

The script runs the agent command on the bash and gets the output. From the output the license name is parsed which is then matched with the correct license name.
There is a dataset of various files containing various open source license statements. 

This dataset is created from nomosTestFiles and spdx-test-files and contains a total of 100 files.
The script runs various commands on each and every files in the test dataset and gives the result in the end. 

## 4. New Algorithm: Semantic Text Similarity
The algorithm is the Gensim Implementation of Doc2Vec algorithm and is used to find the similarity betweent the documents based on its semantic. 

A machine learning model is trained on the training dataset. The model is then loaded which is used to convert the whole document into vectors. The similarity 
