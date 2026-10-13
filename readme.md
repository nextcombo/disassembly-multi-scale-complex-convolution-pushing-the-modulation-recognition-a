# Disassembly | Multi-scale complex convolution, pushing the modulation recognition accuracy up by 3 points [with Python code]

[Drone Signal Detection and Recognition Practical Application]
Disassembly | Multi-scale complex convolution to push the modulation recognition accuracy up by three points
Channel conditions: The presence of multipath, Doppler shifts, and interference among satellites, drones, and ground stations significantly increases the difficulty of recognition in low signal-to-noise ratio (SNR) intervals;
Signal diversity: BPSK, QPSK, 8PSK, QAM16/64, CPFSK, GFSK, AM-DSB/SSB, WBFM, PAM4... Differential modulation styles are subtle, especially between higher order QAM;
Non-cooperative: Without pilot signals or protocol information, blind recognition relies solely on signal characteristics.
Traditional methods rely on manual features (higher order cumulants, cyclic spectra), which have poor generalization under low SNR; although deep learning methods are end-to-end, most networks treat I/Q as two independent real number channels—a core issue that this paper critiques.
Breakthrough Approach: I/Q is a complex number, not two separate real number receiver samples of I/Q signals—it essentially represents a complex signal.
It contains instantaneous amplitude and phase. Both amplitude and phase are coupled, and the differences in modulation methods are precisely reflected in these two aspects.
The conventional practice of real number convolution networks is:
Characteristics
Treating I and Q as two independent channels, losing the joint phase information of the real and imaginary parts during feature extraction. The author believes: retaining the joint distribution of I/Q can alleviate the distortion caused by treating I and Q independently. The solution is - complex convolution.
Complex Convolution: Allowing the network to truly "understand" the mathematical essence of phase complex convolution, moving the rules of complex multiplication into the network. An input complex number, complex convolution kernel, convolution result:
In other words, the outputs of real and imaginary parts are cross-coupled from real part convolution and imaginary part convolution. Corresponding core code (PyTorch segment):
class ComplexConv1d(nn.Module):
4. MCCFN Overall Architecture Dissection
The network is divided into four segments:
IQ Signal (2, 128) | ▼MultiScaleModule (multiple convolutions of 3×1 / 5×1 / 7×1 in parallel, with each having 1/3 channels) | ──► Multi-scale Patch Concatenation, Covering Different Symbolic Periods
Conv Stem (complex convolution blocks ×4: 36→48→64→128→256) | ▼Self-Attention Feature Fusion (2 heads) | ▼Global Average Pooling → Linear(256,512) → Dropout → PReLU → Linear(512, number of classes)
The MultiScaleModule class in TensorFlow defines a neural network architecture for image recognition tasks. Here is the Python code for the class:
3. Feature Fusion with Self-Attention (FFM): The features from multi-scale and convolutional backbone outputs are fused dynamically using multi-head self-attention (2 heads). Attention allows the network to adaptively decide "which scale feature to prioritize" based on the input signals, rather than fixed weight concatenation—this is particularly crucial in high-dynamic SAGIN channels:
Class FeaFusionModule(nn.Module):
Dynamic Dropout: As the learning rate decreases, the model gradually enters a "fine-tuning" phase. At this point, the Dropout rate is progressively increased from the initial 0.3 to the upper limit of 0.6, preventing the network from memorizing noise in the training set during the convergence phase.
if current_lr < self.previous_lr: # Learning rate decrease → Increase regularization
VI. Experimental Results: The three sets of public datasets comprehensively lead in the average recognition accuracy on public datasets:
Dataset
MCCFN average accuracy
Notes
RML2016.10A  
63.415% 11 Class modulation, SNR −20~30 dB
RML2016.10b  
65.29%10 Class modulation, signal longer and "cleaner"
RML22  
69.66%22 Category Expansion Modulation, Challenges the Most
All three datasets exceed the current SOTA. The improvement in the low signal-to-noise ratio range (−10~0 dB) is particularly significant, which is precisely the area of concern for SAGIN scenarios.
VII. Ablation Experiment: Each module is functioning. The author conducted three sets of ablation, each cutting one design:
Single-scale (remove multi-scale, only fixed kernels): Decrease in accuracy → Proves that multi-scale indeed broadens the receptive field coverage;  
Convolution with complex numbers (return to real-number convolution after complex-number convolution): Significant drop in accuracy → Proves that retaining I/Q joint phase is crucial;  
Deletion of attention (from self-attention fusion to simple concatenation): Decrease in accuracy → Proves that dynamic feature fusion is effective.  
Three sets of ablation experiments firmly pin down the necessity of "multi-scale, complex numbers, and attention" designs, which also gives the paper the confidence to pass review.  
Eight, parameter analysis and pitfall avoidance summary:  
Complex number convolution channels should be halved: The internal real/imaginary channels each account for half of the total channels, and the in_channels passed are "real + imaginary", directly using it will report dimension errors;  
2  
Dropout range should not be randomly set: Starting from 0.3 → Upper limit of 0.6 is the range verified through this task, setting the upper limit too high can lead to underfitting, and accuracy may even decrease at low signal-to-noise ratio;  
3  
BN placed after activations: The order of ReLU → BN is trialed through tuning parameters, and training stability is unstable if the order is reversed;  
4  
128-point signals are short samples: RML2016.10A has 128 sampling points per frame, with low information density, overfitting occurs very quickly, and dynamic regularization is almost essential;  
5  
Indicators should not only look at overall accuracy: The variance of accuracy in low SNR and high SNR segments is extremely large, it's necessary to evaluate the model's true ability by looking at curves according to SNR segmentation.
An Li, Yue Li, Qiang Zhang, Ping Chen. "A Multi-scale Complex-valued Convolutional Fusion Network for Automatic Modulation Recognition." Mobile Networks and Applications, 2026. DOI: 10.1007/s11036-026-02513-9 Extended direction: Potential of complex-valued attention and complex Transformer in modulation recognition; transferring the MCCFN to the task of UAV Signal/Radiation Source Identification (SEI); robustness enhancement under complex channel models (Doppler, frequency offset).
Next issue preview: The column "Practical Demonstration of UAV Signal Detection and Identification" will dissect Radiation Source Fingerprint Identification (SEI)—the same complex signal processing, how can features be enhanced to withstand real data collection "crashes".

## Images

![Disassembly | Multi-scale complex convolution, pushing the modulation recognition accuracy up by 3 points [with Python code]](01.jpg)
![Disassembly | Multi-scale complex convolution, pushing the modulation recognition accuracy up by 3 points [with Python code]](02.jpg)
![Disassembly | Multi-scale complex convolution, pushing the modulation recognition accuracy up by 3 points [with Python code]](03.jpg)
![Disassembly | Multi-scale complex convolution, pushing the modulation recognition accuracy up by 3 points [with Python code]](04.jpg)
![Disassembly | Multi-scale complex convolution, pushing the modulation recognition accuracy up by 3 points [with Python code]](05.jpg)
![Disassembly | Multi-scale complex convolution, pushing the modulation recognition accuracy up by 3 points [with Python code]](06.jpg)
![Disassembly | Multi-scale complex convolution, pushing the modulation recognition accuracy up by 3 points [with Python code]](07.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
