---
title: halcon模板匹配入门
categories: ml
tags:
  - halcon
date: 2017-03-07 17:30:10
---

>halcon算子中进行模板匹配的算子主要由create_shape_model 和find_shape_model
* create_shape_model:从局部图像创建模板图像
```c++
create_shape_model(
  Template, //reduce_domain后的模板图像
  NumLevels,//金字塔的层数，可设为“auto”或0—10的整数
  AngleStart,//模板旋转的起始角度
  AngleExtent,//模板旋转角度范围, >=0
  AngleStep,//旋转角度的步长，default:'auto'-> 0< AngleStep<=pi/16
  Optimization,//设置模板优化和模板创建方法
  Metric, //匹配方法设置
  Contrast,//设置对比度
  MinContrast ://设置最小对比度
  ModelID ) //返回值:模板句柄
```
<!--more-->
1. NumLevels越大，找到匹配使用的时间就越小。另外必须保证最高层的图像具有足够的信息（至少四个点）。   
可以通过inspect_shape_model函数查看设置的结果。如果最高层金字塔的消息太少，算法内部会自动减少金字塔层数，    
如果最底层金字塔的信息太少，函数就会报错。如果设为auto，算法会自动计算金字塔的层数，    
我们可以通过get_shape_model_params函数查看金字塔的层数。如果金字塔的层数太大，模板不容易识别出来，    
这是需要将find_shape_model函数中MinScore和Greediness参数设置的低一些。如果金字塔层数太少找到模板的时间会增加。    
可以先使用inspect_shape_model函数的输出结果来选择一个较好的金字塔层数。   
<!--more-->
2. 参数AngleStart、AngleExtent定义了模板可能发生旋转的范围。注意模板在find_shape_model函数中只能找到这个范围内的匹配。   
参数AngleStep定义了旋转角度范围内的步长。如果在find_shape_model函数中没有指定亚像素精度，   
这个参数指定的精度是可以实现find_shape_mode函数中的角度的。参数AngleStep的选择是基于目标的大小的，   
如果模板图像太小不能产生许多不同离散角度的图像，因此对于较小的模板图像AngleStep应该设置的比较大。   
如果AngleExtent不是AngleStep的整数倍, 将会相应的修改AngleStep。如果选择 complete pregeneration，   
不同角度的模板图像将会产生并保存在内存中。用来存储模板的内存与旋转角度的数目和模板图像的的点数是成正比的。   
因此，如果AngleStep太小或是AngleExtent太大, 将会出现该模型不再适合（虚拟）内存的情况。在任何情况下，   
模型是完全适合主存储器的，因为这避免了操作系统的内存分页，使得寻找匹配模板的时间变短。   
由于find_shape_model函数中的角度可以使用亚像素精度，一个直径小于200像素的模板可以选择AngleStep>= 1.    
如果选择AngleStep='auto' (or 0 向后兼容),create_shape_model将会基于模板的大小自动定义一个合适的角度步长.    
 自动计算出来的AngleStep可以使用get_shape_model_params函数查看。    
 如果没有选择complete pregeneration, 该模型会在每一层金字塔上建立在一个参考的位置。    
 这样在find_shape_model函数运行时，该模型必须转化为不同的角度和尺度在运行时在。正因为如此，匹配该模型可能需要更多的时间。

3. 对于特别大的模板图像，将参数Optimization设置为不同于'none'的其他数值是非常有用的。   
如果Optimization= 'none', 所有的模型点将要存储。在其他情况下, 按照Optimization的数值会将模型的点数减少.    
如果模型点数变少了，必须在find_shape_model函数中将参数Greediness设为一个比较小的值,     
比如：0.7、0.8。对于比较小的模型, 减少模型点数并不能提高搜索速度，   
因为这种情况下通常显着更多的潜在情况的模型必须进行检查。如果Optimization设置为'auto'，    
create_shape_model自动确定模型的点数。Optimization的第二个值定义了模型是否进行预处理（pregenerated completely），   
是通过选择'pregeneration'或者'no_pregeneration'来设置的。如果不使用第二个值(例如：仅仅设置了第一个值),     
默认的是系统中的设置，是通过set_system('pregenerate _shape_models',...)来设置的，    
对于默认值是 ('pregenerate_shape_models' = 'false'), 模型没有进行预处理. 模型的预处理设置通常会导致比较低的运行时间，    
因为模型不需要在运行时间时转换。然而在这种情况下,内存的要求和创建模板所需要的时间是比较高的。还应该指出，   
不能指望这两个模式返回完全相同的结果，因为在运行时变换一定会导致变换模型和预处理变换模型之间不同的内部数据。    
比如，如果模型没有 completely pregenerated，在find_shape_model函数中通常返回一个较低的scores，    
这可能需要将MinScore设置成一个较低的值。此外，在两个模型中插值法获得的位置可能略有不同。如果希望是最高精确度，应该使用最小二乘调整得到模型位置。

4. 参数Contras决定着模型点的对比度。对比度是用来测量目标与背景之间和目标不同部分之间局部的灰度值差异。    
Contrast的选择应该确保模板中的主要特征用于模型中。Contrast也可以是两个数值，这时模板使用近似edges_image函数中滞后阈值的算法进行分割。    
这里第一个数值是比较低的阈值，第二个数值是比较高的阈值。Contrast也可以包含第三个，这个数值是在基于组件尺寸选择重要模型组件时所设置的阈值，   
比如，比指定的最小尺寸的点数还少的组件将被抑制。这个最小尺寸的阈值会在每相邻的金字塔层之间除以2。如果一个小的模型组件被抑制，   
但是不使用滞后阈值，然而在Contrast中必须指定三个数值，在这种情况下前两个数值设置成相同的数值。   
这个参数的设置可以在inspect_shape_model函数中查看效果。如果Contrast设置为'auto'，create_shape_model将会自动确定三个上面描述的数值。   
或者仅仅自动设置对比度('auto_contrast')，滞后阈值('auto_contrast_hyst')或是最小尺寸('auto_min_size')中一个。    
其他没有自动设置的数值可以按照上面的格式再进行设置。可以允许各种组合，例如：如果设置 ['auto_contrast','auto_min_size']，   
对比度和最小尺寸自动确定；如果设置 ['auto_min_size',20,30]，最小尺寸会自动设定，而滞后阈值被设为20和30。    
有时候可能对比度阈值自动设置的结果是不满意的，例如，由于一些具体应用的原因当某一个模型组件是被包含或是被抑制时，    
或是目标包含几种不同的对比度时，手动设置这些参数效果会更好。因此对比度阈值可以使用determine_shape_model_params函数自动确定，    
也可以在调用create_shape_model之前使用inspect_shape_mode函数检查效果。

5. 参数Metric定义了在图像中匹配模板的条件。如果Metric= 'use_polarity'，图像中的目标必须和模型具有一样的对比度。   
例如，如果模型是一个亮的目标在一个暗的背景上，那么仅仅那些比背景亮的目标可以找到。如果Metric= 'ignore_global_polarity',    
在两者对比度完全相反时也能找到目标。在上面的例子中，如果目标是比背景暗的也能将目标找到。find_shape_model函数的运行时间在这种情况下将会略微增加。    
如果Metric= ignore_local_polarity', 即使局部对比度改变也能找到模型。例如，当目标包含一部分中等灰度，并且其中部分比较亮部分比较暗时，    
这种模式是非常有用的。由于这种模式下find_shape_model函数的运行时间显著增加，最好的方法是使用create_shape_model创建几个反映目标可能的对比度变化的模型，    
同时使用find_shape_models去匹配他们。上面三个metrics仅仅适用于单通道图像。如果是多通道图像作为模板图像或搜索图像，仅仅第一个通道被使用。    
如果Metric='ignore_color_polarity', 即使颜色对比度局部变化也能找到模型。例如，当目标的部分区域颜色发生变化(e.g.从红到绿)的情况。   
如果不能提前知道目标在哪一个通道是可见的这种模式是非常有用的。在这种情况下find_shape_model函数的运行时间也会急剧增。    
'ignore_color_polarity'可以使用于具有任意通道数目的图像中。如果使用于单通道图像，他的效果和'ignore_local_polarity'是完全相同的。

6. create_shape_model创建的模板通道数目和find_shape_model中的图像通道数目可以是不同的。例如，可以使用综合生成的单通道图像创建模型。    
另外，这些通道不需要是经过光谱细分(像RGB图像)的。这些通道还可以包括具有在不同方向照亮同一个目标所获得的图像。

7. 模型图像Template的domain区域的重心是模板的初始位置，可以在set_shape_model_origin函数中设置不同的初始位置。  


* find_shape_model&find_shape_models 从图像中找到模板匹配到的位置
```c++
find_shape_model(Image : :  //搜索图像
                   ModelID, //模板句柄
                  AngleStart,  // 搜索时的起始角度
                  AngleExtent, //搜索时的角度范围，必须与创建模板时的有交集
                  MinScore, //最小匹配值，输出的匹配的得分Score 大于该值
                  NumMatches, //定义要输出的匹配的最大个数
                  MaxOverlap, //当找到的目标存在重叠时，且重叠大于该值时选择一个好的输出
                 SubPixel, //计算精度的设置，五种模式，多选2，3
                 NumLevels, //搜索时金字塔的层数,为0时继承模板
                Greediness : //贪婪度，搜索启发式，一般都设为0.9，越高速度快,容易出现找不到的情况
                Row, Column, Angle, Score) //输出匹配位置的行和列坐标、角度、得分。
```
 
1. Row、Column的坐标并不是模板在搜索图像中的精确位置，因此不能直接使用他们。    
这些数值是为了创建变换矩阵被优化后的，你可以用这个矩阵的匹配结果完成各种任务，比如调整后续步骤的ROI。  
2. Score是一个0到1之间的数，是模板在搜索图像中可见比例的近似测量。如果模板的一半被遮挡，该值就不能超过0.5。  
3. Image的domain定义了模型参考点的搜索区域，模型参考点是在create_shape_model中用来创建模型的图像的domain区域的重心。  
不考虑使用函数set_shape_model_origin设置不同的初始位置。在图像domain区域的这些点内搜索模型，其中模型完全属于这幅图像。     
这意味着如果模型超出图像边界，即使获得的质量系数(score)大于MinScore也不能找到模型。这种性能可以通过set_system('border_shape_models','true')改变，    
这样那些超出图像边界，质量系数大于MinScore的模型也能找到。这时那些在图像外面的点看作是被遮挡了，可以降低质量系数。在这种模式下搜索的时间将要增加。   
4. 参数AngleStart和AngleExtent确定了模型搜索的旋转角度，如果有必要，旋转的范围会被截取成为create_shape_model函数中给定的旋转范围。      
这意味着创建模型和搜索时的角度范围必须真正的重叠。在搜索时的角度范围不会改变为模2*pi的。为了简化介绍，在该段落剩下的部分所有角度都用度来表示，   
而在find_shape_model函数中使用弧度来设置的。因此，如果创建模板时，AngleStart=-20°、AngleExtent=40°，     
在搜索模板函数find_shape_model中设置AngleStart=350°、AngleExtent=20°，尽管角度模360后是重叠的，还是会找不到模板的。      
为了找到模板，在这个例子中必须将AngleStart=350°改为AngleStart=-10°。
5. 参数MinScore定义模板匹配时至少有个什么样的质量系数才算是在图像中找到模板。MinScore设置的越大,搜索的就越快。   
如果模板在图像中没有被遮挡，MinScore可以设置为0.8这么高甚至0.9。   
6. NumMatches定义了在图像上找到模板的最大的个数。如果匹配时的质量系数大于MinScore的目标个数多于NumMatches，   
仅仅返回质量系数最好的NumMatches个目标位置。如果找的匹配目标不足NumMatches，那么就只返回找到的这几个。参数MinScore优于NumMatches。   
7. 如果模型具有对称性，会在搜索图像的同一位置和不同角度上找到多个与目标匹配的区域。参数MaxOverlap是0到1之间的，     
定义了找到的两个目标区域最多重叠的系数，以便于把他们作为两个不同的目标区域分别返回。如果找到的两个目标区域彼此重叠并且大于MaxOverlap，      
仅仅返回效果最好的一个。重叠的计算方法是基于找到的目标区域的任意方向的最小外接矩形(看smallest_rectangle2)。如果MaxOverlap=0,       
找到的目标区域不能存在重叠, 如果MaxOverla p=1，所有找到的目标区域都要返回。   
8. SubPixel确定找到的目标是否使用亚像素精度提取。如果SubPixel设置为'none'(或者'false' 背景兼容)，模型的位置仅仅是一个像素精度和在create_shape_model中   
定义的角度分辨率。如果SubPixel设置为'interpo lation'(或'true')，位置和角度都是亚像素精度的。在这种模式下模型的位置是在质量系数函数中插入的，这种模式几乎   
不花费计算时间，并且能达到足够高的精度，被广泛使用。然而在一些精度要求极高的应用中，模板的位置应该通过最小二乘调整决定，比如通过最小化模板点到相关图像点的距离。  
与 'interpolation'相比，这种模式需要额外的计算时间。对于最小二乘调整的模式有：'least_squares', 'least_squares_high', 和'least_squares_very_high'。   
他们可用来定义被搜索的最小距离的精度，选择的精度越高，亚像素提取的时间越长。然而，通常SubPixel设置为'interpolation'。如果希望设置最小二乘就选择'least_squares'，   
因为这样才能确保运行时间和精度的权衡。     
9. NumLevels是在搜索时使用的金字塔层数，如有必要，层数截成创建模型时的范围。如果NumLevels=0，使用创建模板时金字塔的层数。另外NumLevels还可以包含第二个参数，    
这个参数定义了找到匹配模板的最低金字塔层数。NumLevels=[4,2]表示匹配在第四层金字塔开始，在第二层金字塔找到匹配（最低的设为1）。   
可以使用这种方法降低匹配的运行时间，但是这种模式下位置精度是比正常模式下低的，所谓正常模式是在金字塔最底层匹配。因此如果需要较高的精度，    
应该设置SubPixel至少为'least_squares'。如果金字塔最底层设置的过大，可能不会达到期望的精度，或者找到一个不正确的匹配区域。    
这是因为在较高层的金字塔上模板是不够具体的，不足以找到可靠的模板最佳匹配。在这种情况下最低金字塔层数应设为最小值。   
10. 参数Greediness确定在搜索时的“贪婪程度”。如果Greediness=0，使用一个安全的搜索启发式，只要模板在图像中存在就一定能找到模板，   
然而这种方式下搜索是相对浪费时间的。如果Greediness=1，使用不安全的搜索启发式，这样即使模板存在于图像中，也有可能找不到模板，    
但只是少数情况。如果设置Greediness=0.9，在几乎所有的情况下，总能找到模型的匹配。   
