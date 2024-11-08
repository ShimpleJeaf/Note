**-c 编码 -c:a音频编码 -c:v视频编码**
-map input_file_index:stream_type_specifier:stream_index.
**音视频合并**                                    输入0的视频  输入1的音频
ffmpeg -i video.mp4 -i audio.mp3 -c copy -map 0\:v:0 -map 1\:a:0 videoWithAudio.mp4 
**提取音频**
ffmpeg -i videoWithAudio.mp4 -vn -acodec copy onlyAudio.aac 
**删除音频**                      视频复制    音频不用
ffmpeg.exe -i videoWithAudio.mp4 -c:v copy -an videoWithoutAudio.mp4

**视频合并**

ffmpeg -i "concat:input1.mpg|input2.mpg|input3.mpg" -c copy output.mpg

###### 分解成图片

ffmpeg -i test.mp4 -r 10 -f image2 %05d.jpg

###### 图片合并成视频

ffmpeg -f image2 -i %d.jpeg output.mp4

###### 视频截取

ffmpeg -i input.mp4 -ss 00:00 -to 1:12 -c copy output.mp4

ffmpeg -i input.mp4 -ss 00:00 -t 10 -c copy output.mp4

###### [n] Masked training ( y/n ?:help ) : ?

This option is available only for 'whole_face' or 'head' type. Masked training clips training area to full_face mask or XSeg mask, thus network will train the faces properly.

###### [y] Eyes and mouth priority ( y/n ?:help ) : ? Helps to fix eye

problems during training like "alien eyes" and wrong eyes direction. Also makes the detail of the teeth higher.

###### [n] Uniform yaw distribution of samples ( y/n ?:help ) : ?

Helps to fix blurry side faces due to small amount of them in the faceset.

###### [y] Blur out mask ( y/n ?:help ) : ?

Blurs nearby area outside of applied face mask of training samples. The result is the background near the face is smoothed and less noticeable on swapped face. The exact xseg mask in src and dst faceset is required.

###### [n] Place models and optimizer on GPU ( y/n ?:help ) :

###### [y] Use AdaBelief optimizer? ( y/n ?:help ) : ?

Use AdaBelief optimizer. It requires more VRAM, but the accuracy and the generalization of the model is higher.

###### [y] Use learning rate dropout ( n/y/cpu ?:help ) : ?

When the face is trained enough, you can enable this option to get extra sharpness and reduce subpixel shake for less amount of iterations. Enabled it before \`disable random warp\` and before GAN.
n - disabled.
y - enabled
cpu - enabled on CPU. This allows not to use extra VRAM, sacrificing 20% time of iteration.

###### [n] Enable random warp of samples ( y/n ?:help ) : ?

Random warp is required to generalize facial expressions of both faces. When the face is trained enough, you can disable it to get extra sharpness and reduce subpixel shake for less amount of iterations.

###### [0.1] Random hue/saturation/light intensity ( 0.0 .. 0.3 ?:help ) : ? src 必须完全剔除背景才能用，不然会色彩异常

Random hue/saturation/light intensity applied to the src face set only at the input of the neural network. Stabilizes color perturbations during face swapping. Reduces the quality of the color transfer by selecting the closest one in the src faceset. Thus the src faceset must be diverse enough. Typical fine value is 0.05

###### [0.5] GAN power ( 0.0 .. 5.0 ?:help ) : ?

Forces the neural network to learn small details of the face. Enable it only when the face is trained enough with lr_dropout(on) and random_warp(off), and don't disable. The higher the value, the higher the chances of artifacts. Typical fine value is 0.1

###### [1.0] Face style power ( 0.0..100.0 ?:help ) : ?

Learn the color of the predicted face to be the same as dst inside mask. If you want to use this option with 'whole_face' you have to use XSeg trained mask. Warning: Enable it only after 10k iters, when predicted face is clear enough to start learn style. Start from 0.001 value and check history changes. Enabling this option increases the chance of model collapse.

###### [1.0] Background style power ( 0.0..100.0 ?:help ) : ?

Learn the area outside mask of the predicted face to be the same as dst. If you want to use this option with 'whole_face' you have to use XSeg trained mask. For whole_face you have to use XSeg trained mask. This can make face more like dst. Enabling this option increases the chance of model collapse. Typical value is 2.0

###### [rct] Color transfer for src faceset ( none/rct/lct/mkl/idt/sot ?:help ) : ?

Change color distribution of src samples close to dst samples. Try all modes to find the best.

###### [y] Enable gradient clipping ( y/n ?:help ) : ?

Gradient clipping reduces chance of model collapse, sacrificing speed of training.

###### [n] Enable pretraining mode ( y/n ?:help ) : ?

Pretrain the model with large amount of various faces. After that, model can be used to train the fakes more quickly. Forces random_warp=N, random_flips=Y, gan_power=0.0, lr_dropout=N, styles=0.0, uniform_yaw=Y

##### 训练时

mask training + random hue 0.05(必须和mask training一起使用)

random warp

random warp + rate dropout

eyes and mouth priority

gan 0.1

gan 0.1 + face style 0.001

##### 使用时

blur out mask + backgroud style
