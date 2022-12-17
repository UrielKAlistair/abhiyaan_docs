# EncoderCovariance
Takes encoder data and updates the position and position covariance of the robot by publishing an odometry message.

vajra_tiva.py is the functional python code and the only important file in this package. The rest are a framework used for testing it and/or older code.

## General Information

Encoders are incrementally measuring instruments, so their measurements drift with time.  

Encoders give pulses of data which some constant can convert into no of wheel revolutions.  
In the present setup in vajra, each square wave pulse triggers 4 counts, and 1024 pulses constitute one revolution of the encoder.  
There is some gear ratio from the encoder to the tyre, so a revolution of the encoder is only 4/5 revolutions of the tyre.  

That aside, once we get encoder data, at least in the case of vajra, we can deduce the vehicle's position.  
In the case of a vehicle like bolt, we cannot determine the vehicle heading by looking at the rear wheel encoders. The stepper motor we have controls the steering angle.  

Doing this is fairly simple. Differential Drive kinematic equations are straightforward.  
However, having these equations at hand does not mean that you can do encoder odometry well directly. This is because the model uses the wheel radius and wheelbase distance, both of which typically do not match real life measurements. The model works best with separate values, which we need to tune and figure out. Once this is done, pose estimation works perfectly.  

Tuning the wheelbase and wheel radius is a pain.  
First, a rough tuning must be done. Make the vehicle travel a set distance (use a PID) and measure how much it is irl. Fix the radius accordingly.
Make the vehicle do a whole number of on spot 360 rotations using a teleop, and use the distance obtained from the radius and encoder data to deduce the wheelbase.  

Next, a fine tuning can be done using the method prescribed in this paper: http://www-personal.umich.edu/~johannb/Papers/paper60.pdf  
This involves making the vehicle go on a square loop clockwise and anticlockwise several times, and measuring the coordinates of the initial and final positions. We have an almost complete implementation of this test in this repo: https://github.com/UrielKAlistair/encoder_testing.git  
However, we stopped coding this when we realised that our errors were far too large to use the methods prescribed in the paper. They run several square loops, 4 or 5, and still see errors in the order of millimetres. We got 30 cm errors in one loop. It's still very good for our purposes, but the possibility of this UMBmark tuning exists.  

Now that we have taken care of the position, we must worry about the confidence of this measurement. This is given by the covariance matrix. If you don't know what that means (skip this if you do), A normal gaussian distribution has 2 parameters, mean and variance. In n Dimensions, we can still define a gaussian distribution, and the parameters of this are filled into the covariance matrix. The diagonal entries give variances of individual parameters. (If we have a 3D gaussian over x, y and yaw, the diagonal entries are the variance of x,y and yaw.) Several sensors we have give the vehicle's position along with their covariances, and these uncertainties are weighed against each other to determine with significantly better accuracy and certainty, the position of the vehicle. This merging of several sensors' data to give a single measurement is done by a Kalman Filter.  

The covariance of the position has always been a pain for us. Previously, it was assumed to be some jugaad values. Now, we have a few partially done things before us:  
- In the above mentioned repo, there is some code I wrote which does this, following this paper: https://drive.google.com/file/d/1SywLYtnIfx5PKHOlXF9dQt7bJXURntXA/view?usp=sharing  
This code doesn't work well at all. I don't know what I am doing wrong. The variances in the diagonal entries go negative, and some garbage values are seen.  
- Lalit transcribed some rust code written for a Roomba from https://github.com/Sollimann/CleanIt/blob/main/autonomy/src/slam/README.md 
 into this repo: https://github.com/NuminousLozenge/Odometry-Error-model.git  
- The robot_localisation package might have something relevant. It was mentioned in the roscon talk about localisation, but I haven't looked into how to implement it yet.  
