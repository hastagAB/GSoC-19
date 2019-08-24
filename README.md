[![HitCount](http://hits.dwyl.io/hastagAB/GSoC-19.svg)](http://hits.dwyl.io/hastagAB/GSoC-19)
![Atarashi](https://img.shields.io/pypi/status/atarashi) 
[![Build Status](https://travis-ci.com/fossology/atarashi.svg?branch=master)](https://travis-ci.com/fossology/atarashi)
![License](https://img.shields.io/pypi/l/atarashi)
![GitHub stars](https://img.shields.io/github/stars/fossology/atarashi?style=social)




                               _______       _____             _____ _    _ _____ 
                            /\|__   __|/\   |  __ \     /\    / ____| |  | |_   _|
                           /  \  | |  /  \  | |__) |   /  \  | (___ | |__| | | |  
                          / /\ \ | | / /\ \ |  _  /   / /\ \  \___ \|  __  | | |  
                         / ____ \| |/ ____ \| | \ \  / ____ \ ____) | |  | |_| |_ 
                        /_/    \_\_/_/    \_\_|  \_\/_/    \_\_____/|_|  |_|_____|
                                                                  
                                                           

# 🚩 ABSTRACT
Atarashi scans for license statements in open source software, focusing on text statistics and information retrieval algorithms. It was designed to work stand-alone and with FOSSology Software. 
Earlier, it worked only on the simple command-line interface. for it's better usage it needed to be integrated with the fossology software.

My project was to integrate Atarashi with the fossology software and to create a stable GUI that uses various existing fossology features. The integration part was done using Ninka, which was used to wrap atarashi around fossology.

To improve atarashi and continue its development, I had to make a pip package of atarashi and publish it to PyPI. Along with that, I had to establish a machine learning model and implement a new algorithm using it. The new algorithm **Semantic Text Similarity** works on the [Gensim](https://radimrehurek.com/gensim/models/doc2vec.html) implementation of [Doc2Vec](https://cs.stanford.edu/~quocle/paragraph_vector.pdf) and finds the most similar docs using the cosine similarities of vectors. With the gradual improvement in the future will make this model more accurate and atarashi more powerful and faster than ever. 

Also, I had to create an **Evaluation Script** for the existing and upcoming algorithms for atarashi to validate its accuracy and reliability. Evaluation is based on time and the accuracy of the agents. This will be beneficial to get the best open-source license scanning algorithms.


# 🌏 CONTRIBUTIONS 
## 1. Package Atarashi and Publish to PyPI
To integrate Atarashi with fossology we had to make a package of it and publish it to PyPI so that we can pip install atarashi in the fossology system and use that in the software. To make a publishable python package there needs to be certain organized directory and file system. The structure of Atarashi was already organized and setup.py had every bit of detailed which was needed to publish. This saved me a lot of time and work.

There were some minor errors like the classifiers in setup.py metadata was a little bit different than actually standardized classifiers provided by PyPI. Changed  ``Development Status ::  Pre-Alpha" to “Development Status :: 2 - Pre-Alpha”`` and
``"License :: OSI Approved :: GPL v2.0 License" to “License :: OSI Approved :: GNU General Public License v2 (GPLv2)``. 
One more error occurred while publishing when it was not accepting the CodeComment as it was external so I had to remove it from setup.py. 

Currently, the Package is live at: https://pypi.org/project/atarashi/ 

Atarashi can be installed in the system using `pip install atarashi`

## 2. Integrating Atarashi to Fossology
To integrate Atarashi with fossology Ninka Agent was to be modified and used as a wrapper. The codebase was to be modified accordingly with respect to atarashi. 
### The Modifications are as follows: 
* Changed various Classes and Functions accordingly. 
* Added the command of atarashi agent to run on bash. 
* Parsed the output format of Atarashi i.e. JSON to the result output format of Ninka. for that, I used Jsoncpp library that was already used there is FOSSology before.
* Modified the makefiles accordingly
* Compiled atarashiWrapper codebase and connected to Fossology database.
* Added atarashiWrapper to fossology Scheduler.

The working UI was fixed by [GMishx](https://github.com/GMishx)
* Cherry Picked the commit [GMishx@3b31c17](https://github.com/GMishx/fossology/commit/3b31c17996691c5b3beebe90506876efb5f361dc) and added to the PR for working UI
* Added the mod_deps files for Python, pip and atarashi Installation.

The UI is working fine and the agent can be scheduled for `upload < for file < Select optional analysis`

![atarashiWrapper](/files/atarashi-UI.png)

`To test, just do a fresh installation of Fossology and upload a file with Atarashi license scanning option.`

The PR for the integration is: [feat(atarashi): Add Atarashi to FOSSology](https://github.com/fossology/fossology/pull/1408)

## 3. Algorithms Evaluator
Before creating any new algorithm for Atarashi, it was suggested that a script should be there to evaluate the algorithms to get an accurate and reliable license scanner agent. Also it will be easier to get the best scanner agent out of all. 

The evaluation is done on the basis of two factors:
1. Time taken by the agent
2. Accuracy of the result

The script runs the agent command on the bash and gets the output. From the output the license name is parsed which is then matched with the correct license name.
There is a dataset of various files containing various open source license statements. 

This dataset is created from nomosTestFiles and spdx-test-files and contains a total of 100 files.
The script runs various commands on each and every files in the test dataset and gives the result in the end. 

The PR for the evaluator is: [feat(evaluator): Add Evaluation Script for algorithms](https://github.com/fossology/atarashi/pull/57)

## 4. New Algorithm: Semantic Text Similarity
**Semantic Text Similarity** find the similarity between documents based on its semantics. 

The [Gensim](https://radimrehurek.com/gensim/models/doc2vec.html) implementation of [Doc2Vec](https://cs.stanford.edu/~quocle/paragraph_vector.pdf) converts the whole document (unlike word2vec) into vector with their labels.
The Doc2Vec model is trained using the filename as its label and license text as the document. 
The current training dataset is the txt format of  [license-list-data](https://github.com/spdx/license-list-data) provided by SPDX. 

The model is trained on the full license training dataset using the filename as its label and license text as the document. The model is then loaded which is used to convert the whole document into vectors.

The cosine distance between the vectors are calculated and the highest score is returned as sim_score.

### Steps in training the model
1. Loads the dataset
2. Reads each document and save its content in memory.
3. Tokeninze the content and lowercase it.
4. Saves each and every token as list elements.
5. Iterate over the whole tokens of a document and provided it with a label.
6. starts the training with fixed epoch size and learning rate.
7. Saves the model in binary format

### Steps in Implementing the Algorithm
1. Loads the trained model.
2. Load and reads the document provided in the filepath.
3. Tokenize the whole document and lowercase it.
4. Saves each and every token as list elements.
5. finds the cosine sim between the provided document and that of the trained model.
6. Returns the top 10 similar document labels.
7. The label with the highest sim_score is the result.

> The label is actually the license name 

The PR for the Algorithm is: [feat(doc2vec) : Semantic Text Similarity Algorithm with dataset & training code](https://github.com/fossology/atarashi/pull/60)


# 🔧 PULL REQUESTS
## Major Contributions
- [feat(atarashi): Add Atarashi to FOSSology](https://github.com/fossology/fossology/pull/1408)
- [feat(evaluator): Add Evaluation Script for algorithms](https://github.com/fossology/atarashi/pull/57)
- [feat(doc2vec) : Semantic Text Similarity Algorithm with dataset & training code](https://github.com/fossology/atarashi/pull/60)

## Other Contributions
- [fix(obligations): fix UI and connected db to dropdown menu](https://github.com/fossology/fossology/pull/1329)
- [style(atarashii): formatting json output](https://github.com/fossology/atarashi/pull/56)
- [feat(atarashii): converted Json output for all agents](https://github.com/fossology/atarashi/pull/55)


# 👨🏻‍🏫 DELIVERABLES

| Tasks   | Planned | Completed     |
| :---:       |    :----:   |    :---:      |
| Package Atarashi     | Yes       | :heavy_check_mark:   |
| Publish to PyPI   | Yes        | :heavy_check_mark:      |
| Integrate Atarashi with FOSSology| Yes | :heavy_check_mark: |
| Create working UI for Atarashi | Yes | :heavy_check_mark: |
| Create Algorithm Evaluator | No | :heavy_check_mark: |
| Training and Implementing New ML Algorithm | Yes but Imporoved | :heavy_check_mark: |


# 🚀 FUTURE PLANS
1. Increase the size and Quality of training Dataset for ML Model
2. Improve text preprocessing using third party libraries
3. Work on code comment extractor
4. Parallelize the evaluation script
5. Maintain the Atarashi package published at PyPI


# 📚 Things I learned from Google Summer of Code
* Learned about Real-World Software system workflow and Architecture.
* various Open-Source licenses and their Importance in codes, projects and softwares.
* Started understanding a bit of PHP and C/C++
* Pacakaging of Python Projects and how it is maintained and released.
* Shell scripts and its use within various systems
* Sharpened my skill of GIT
* Various Information retrieval algorithms especially word2vec and doc2vec.
* Learned to create a better and cleaner dataset.
* Sharpened my knowledge of Machine Learning.
* Learned the importance of time management as well as perfect deliverables.
* Learned the importance of docmentation.
* Improved my communication skill.

# 📜 License
This repository is licensed under the [GPL-2.0](/LICENSE) © [HastagAB](https://github.com/hastagAB).

[![](https://img.shields.io/badge/Made%20With%20❤️%20By-HastagAB-red)](https://github.com/hastagAB)
