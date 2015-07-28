import numpy as np
import matplotlib.pyplot as plt
import random



# repetition number of a sequence
rept = 1

# max and min value of triangle
tri_max = 0.8
tri_min = -0.8

# max and min value of sin curve
sin_max = 0.8
sin_min = -0.8

# length of sound (triangles)
#length_tri = 10   #tri and sin end at the same time
#length_tri = 8    #tri end before sin
length_tri = 12   #tri end after sin

# length of action (sin curve)
length_sin = 20

#number of sequence
sequence_number = 3

# define finite state machine
sequence_0 = [1, 2, 1, 2, 3, 4]
sequence_1 = [1, 4, 3, 2, 1, 2]
sequence_2 = [1, 3, 2, 4]





# fuction to choose sequence type
def sequence_type(i):
    sequence = [] 
    if i == 0:
        sequence = sequence_0
    if i == 1:
        sequence = sequence_1
    if i == 2:
        sequence = sequence_2
    return sequence


# function to select primitive length
def select_primitive_length(delay, length_tri, length_sin):
    if 2 * length_tri == length_sin:
        length = delay + length_sin
    elif 2 * length_tri < length_sin:
        length = delay + length_sin
    elif 2 * length_tri > length_sin:
        length = delay + (length_sin/2) + length_tri
    return length


# fuction to get triangle wave 
def triangle_wave(length_tri, trimax, trimin, delay, length_sin, length):
    tri = np.array([[0]])
    for x in xrange(length):
        if 0 <= x < delay + length_sin/2:
            y = trimin
            
        elif delay + length_sin/2 <= x < delay + length_sin/2 + length_tri/2:
            y = (trimax - trimin) * (x - delay - length_sin/ 2) / (length_tri/2) + tri_min
            
        elif delay + length_sin/2 + length_tri/2 <= x < delay + length_sin/2 + length_tri:
            y = (trimax - trimin) * (delay + length_sin/2 + length_tri - x) / (length_tri/2) + tri_min
                
        elif delay + length_sin/2 + length_tri <= x < length:
            y = trimin

        tri = np.r_[tri, np.array([[y]])]
        
    return tri[1:, :]


# function to select vision from bell postion (in front of robot)
def select_vision(length):
    if left == 1:
        vision = 0.8 * np.ones(length)
    elif left ==2:
        vision = 0.8 / 3 * np.ones(length)
    elif left == 3:
        vision = - 0.8 /3 * np.ones(length)
    elif left == 4:
        vision = - 0.8 * np.ones(length)
    return vision


# function to get action for left arm
def arm_l(length, delay, length_sin, sin_max, sin_min):
    action_l = np.array([[0]])
    for x in xrange(length):
        if x < delay:
            y = sin_min
        elif delay <= x < (delay + length_sin):
            theta = np.pi * (x - delay) / float(length_sin)
            y = (sin_max - sin_min) * np.sin(theta) + sin_min
        elif (delay + length_sin) <= x < length:
            y = sin_min
            
        action_l = np.r_[action_l, np.array([[y]])]
            
    action_l = action_l[1:, :]
    return action_l
    
# function to select action for left arm
def select_action_l(state, delay, length, length_tri, length_sin, sin_min):
    action_l = np.array([[0]])
    if (left == 1):
        if (state == 1):
            action_l = arm_l(length, delay, length_sin, sin_max, sin_min)
        else:
            action_l = sin_min * np.ones(length)
            
    if (left == 2):
        if (state == 2):
            for x in xrange(length):
                 action_l = arm_l(length, delay, length_sin, sin_max, sin_min)
        else:
            action_l = sin_min * np.ones(length)

    if (left == 3):
        if (state == 3):
            for x in xrange(length):
                 action_l = arm_l(length, delay, length_sin, sin_max, sin_min)
        else:
            action_l = sin_min * np.ones(length)
            
    if (left == 4):
        if (state == 4):
            for x in xrange(length):
                 action_l = arm_l(length, delay, length_sin, sin_max, sin_min)
        else:
            action_l = sin_min * np.ones(length)
            
    return action_l
            

# fuction to get action for right arm
def arm_r(length, delay, length_sin, sin_max, sin_min):
    action_r = np.array([[0]])
    for x in xrange(length):
        if x < delay:
            y = - sin_min
        elif delay <= x < (delay + length_sin):
            theta = np.pi * (x - delay) / float(length_sin)
            y = - (sin_max - sin_min) * np.sin(theta) - sin_min
        elif (delay + length_sin) <= x < length:
            y = - sin_min
            
        action_r = np.r_[action_r, np.array([[y]])]
            
    action_r = action_r[1:, :]

    return action_r
    
# function to select action for right arm
def select_action_r(state, delay, length, length_tri, length_sin, sin_min):
    action_r = np.array([[0]])
    if (right == 1):
        if (state == 1):
            action_r = arm_r(length, delay, length_sin, sin_max, sin_min)
        else:
            action_r = - sin_min * np.ones(length)
            
    if (right == 2):
        if (state == 2):
            for x in xrange(length):
                 action_r = arm_r(length, delay, length_sin, sin_max, sin_min)
        else:
            action_r = - sin_min * np.ones(length)

    if (right == 3):
        if (state == 3):
            for x in xrange(length):
                 action_r = arm_r(length, delay, length_sin, sin_max, sin_min)
        else:
            action_r = - sin_min * np.ones(length)
            
    if (right == 4):
        if (state == 4):
            for x in xrange(length):
                 action_r = arm_r(length, delay, length_sin, sin_max, sin_min)
        else:
            action_r = - sin_min * np.ones(length)
            
    return action_r


# function to select primitive from a given state
def select_primitive(state):
    
    delay = random.randint(0,5)
    #delay = 5
    length = select_primitive_length(delay, length_tri, length_sin)
    theta = 2 * np.pi * np.arange(length) / length
    vision_l = select_vision(length)
    vision_r = select_vision(length)
    action_l = select_action_l(state, delay, length, length_tri, length_sin, sin_min)
    action_r = select_action_r(state, delay, length, length_tri, length_sin, sin_min)
    
    if state == 1:
        sound_1 = triangle_wave(length_tri, tri_max, tri_min, delay, length_sin, length)
        sound_2 = tri_min * np.ones(length)
        sound_3 = tri_min * np.ones(length)
        sound_4 = tri_min * np.ones(length)

    elif state == 2:
        sound_1 = tri_min * np.ones(length)
        sound_2 = triangle_wave(length_tri, tri_max, tri_min, delay, length_sin, length)
        sound_3 = tri_min * np.ones(length)
        sound_4 = tri_min *  np.ones(length)
        
    elif state == 3:
        sound_1 = tri_min * np.ones(length)
        sound_2 = tri_min * np.ones(length)
        sound_3 = triangle_wave(length_tri, tri_max, tri_min, delay, length_sin, length)
        sound_4 = tri_min * np.ones(length)

    elif state == 4:
        sound_1 = tri_min * np.ones(length)
        sound_2 = tri_min * np.ones(length)
        sound_3 = tri_min * np.ones(length)
        sound_4 = triangle_wave(length_tri, tri_max, tri_min, delay, length_sin, length)
        
    # concatenate 3 columns to make a matrix
    # [s] [v] [a]   : 3 columns ->
    # [[s] [v] [a]] : 1 matrix
    
    primitive = np.c_[sound_1, sound_2, sound_3, sound_4, vision_l, vision_r, action_l, action_r]
    return primitive


# variable to save primitives
name_num = 0

for left in [1,2,3,4]:
    for right in [1,2,3,4]:
        if left == right:
            continue
        for x in xrange(sequence_number):
            data = np.array([[0, 0, 0, 0, 0, 0, 0, 0]]) # when data are saved, these zeros are ignored
            sequence = sequence_type(x)
            for _ in xrange(rept):
                for state in sequence:
                    primitive = select_primitive(state)
                    data = np.r_[data, primitive]
                     # concatenate 2 rows to make a matrix
                     # [] [] : 2 rows
                     # [[]
                     #  []]  : 1 matrix
   
            # save data from 1st to the last line (only 0th line is ignored)       
            data = data[1:, :]
            #filename = "target" + str(name_num) + ".txt"
            #np.savetxt(filename, data, fmt = "%.6f", delimiter = "\t")
            #name_num += 1
            id_s = str(name_num)
            id_s = id_s.rjust(6, "0")
            filename =  "target" + id_s + ".txt"
            print filename, "\n"
            np.savetxt(filename, data, fmt = "%.6f", delimiter = "\t")
            name_num += 1
            
            
#################################################################
# plot data
            plt.plot(np.arange(data.shape[0]), data[:, 0], marker = "+", label = "Sound_1")
            plt.plot(np.arange(data.shape[0]), data[:, 1], marker = "+", label = "Sound_2")
            plt.plot(np.arange(data.shape[0]), data[:, 2], marker = "+", label = "Sound_3")
            plt.plot(np.arange(data.shape[0]), data[:, 3], marker = "+", label = "Sound_4")
            plt.plot(np.arange(data.shape[0]), data[:, 4], marker = "+", label = "Vision_l")
            plt.plot(np.arange(data.shape[0]), data[:, 5], marker = "+", label = "Vision_r")
            plt.plot(np.arange(data.shape[0]), data[:, 6], marker = "+", label = "Action_l")
            plt.plot(np.arange(data.shape[0]), data[:, 7], marker = "+", label = "Action_r")

            plt.legend(loc = "best")
#plt.xlim(0,200)
            plt.xlabel("Time step")  
            plt.ylabel("Scaled target value")
            plt.ylim(-1, 1)

            plt.grid(True)
            plt.show()
#################################################################

