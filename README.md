vdpgm_stack
===========

Provides ROS Interface to Kenichi Kurihara's matlab code for Variational Dirichlet Process Gaussian Mixture Models

Installation Instructions
=========================
Install matlab revision R2012b or the equivalent MCR.

Make sure you are using java jdk 6 (rosjava_core currently will not build with jdk 7).

Assuming you are using groovy on ubuntu, and have not yet installed rosjava, do the following:

    sudo apt-get install python-pip
    sudo pip install --upgrade rosinstall
    mkdir ~/my_workspace
    cd ~/my_workspace
    rosws init
    rosws merge /opt/ros/groovy/.rosinstall
    rosws merge http://rosjava.googlecode.com/hg/.rosinstall
    rosws merge https://raw.github.com/lteacy/vdpgm_stack/master/rosinstall
    rosws update
    source setup.bash

Now build things in the following order

    rosmake vdpgm_msgs
    roscd rosjava_core
    ./gradlew install

To run tests on rosjava, do the following in the same directory

    ./gradlew test

Now build the vdpgm package:

    roscd vdpgm_srv
    make

Test installation
=================
To run and test the installation, do the following

    roscd vdpgm_srv
    roslaunch launch/imm_server.launch
    
In a separate terminal, do:

    source ~/my_workspace/setup.bash
    roscd vdpgm_srv
    rostopic pub /vdpgm/data vdpgm_msgs/DataStamped -f testData.yaml
    rosservice call /vdpgm/get_imm

You should see parameters for the fitted gaussian mixture printed out in yaml format.

Modifying MATLAB code
=====================
Note that the matlab code is prebuild using javabuilder for Matlab R2012b.
To use a different version of matlab, or change the behaviour of the matlab code, you must manually use matlab jbuilder
to replace the jar files in the jars directory.

Future Work
===========
Possible improvements to think about in the future:
* Modify server to maintain separate models in parallel. At the moment this might be achieved by running separate
  server instances, but these would each have a separate matlab runtime environment. Might be more efficient to share
  a single instance.
* Allow prior model to be specified using ros parameter service, or ros messages. This would require support in the
  matlab code. In particular, we might want to specify different priors for the covariance of each component, rather
  than let the model choose this for itself. This would also allow the model to work with no prior data.
* Rewrite the whole thing in python. This would be much more elegant, but would require a rewrite of Kenichi Kurihara's
  matlab code. Might be able to use this: http://ompc.juricap.com

References
==========

* Kenichi Kurihara, Max Welling and Yee Whye Teh,
  Collapsed Variational Dirichlet Process Mixture Models,
  the Twentieth International Joint Conference on Artificial Intelligence (IJCAI 2007). 

* Kenichi Kurihara, Max Welling and Nikos Vlassis,
  Accelerated Variational Dirichlet Mixture Models,
  Advances in Neural Information Processing Systems 19 (NIPS 2006). 

* David M. Blei and Michael I. Jordan, Variational Inference for Dirichlet Process Mixtures,
  Bayesian Analysis, Vol.1, No.1, 2005.
