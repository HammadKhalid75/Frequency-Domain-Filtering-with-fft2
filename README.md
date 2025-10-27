## Lab 3: Frequency-Domain Filtering with `fft2`

This repository contains the MATLAB code and results for the Image Processing lab focused on frequency-domain filtering using the 2D Fast Fourier Transform FFT.

The core concept demonstrated is the **Convolution Theorem**, which states that convolution in the spatial domain is equivalent to multiplication in the frequency domain. This is used to implement Low-Pass ($\text{LP}$) and High-Pass ($\text{HP}$) filters.

***

### Code Implementation Details

The MATLAB script (provided in the prompt) loads an image (`peppers.png` or `cameraman.tif`), converts it to grayscale and double precision, and performs the following steps:

#### 1. Magnitude Spectrum
* The 2D FFT is computed F=fft2I.
* The zero-frequency component is shifted to the center Fshift=fftshiftF.
* The **log-magnitude spectrum** is calculated and displayed to visualize the frequency content. Low frequencies appear bright in the center.

#### 2. Filter Generation
* Two types of LP filters are created in the frequency domain:
    * **Ideal Low-Pass Filter H_ideal_LP:** A binary "brick wall" filter that cuts off all frequencies beyond a specified radius D0=40.
    * **Gaussian Low-Pass Filter H_gauss_LP:** A smooth, continuous filter defined by a Gaussian function with a standard deviation sigma=20.

#### 3. Low-Pass Filtering
* The filters are applied by element-wise multiplication with the centered FFT: G = ifft2ifftshiftH.Fshift.
* **Result:** The ideal filter causes visible **ringing artifacts** (Gibbs phenomenon), while the Gaussian filter produces a smooth blurring effect.

#### 4. High-Pass Filtering
* A Gaussian HP filter H_gauss_HP is created as the **complement** of the LP filter 1- H_gauss_LP.
* Applying the HP filter enhances edges and fine details (high frequencies) and removes smooth areas (low frequencies).

#### 5. Spatial vs. Frequency Domain Comparison
* A Gaussian filter is applied in the spatial domain using convolution imfilter.
* The spatial result is compared visually to the frequency-domain Gaussian result. As expected from the Convolution Theorem, the results are nearly **identical**, validating the frequency-domain approach.

***

### Reflections

#### 1) Why does ideal LP cause ringing (Gibbs phenomenon)?
The **Ideal Low-Pass Filter** has a **sharp, discontinuous cutoff** in the frequency domain. By the Convolution Theorem, this abrupt truncation in frequency corresponds to a **sinc function** in the spatial domain. When convolving this sinc function with sharp edges in the image, the sinc function's oscillating side lobes cause **overshoot and undershoot** of the signal near the discontinuity, which manifests visually as the **ringing artifact**.

#### 2) What does `fftshift` do visually?
The standard fft2 function places the **DC component** (zero frequency, the average intensity) at the **top-left corner** of the output matrix. fftshift **rearranges the quadrants** of the FFT matrix to move this DC component to the **center** of the image. This places the low frequencies in the center and the high frequencies toward the edges, creating a standard, visually interpretable, symmetric magnitude spectrum.

#### 3) When is frequency-domain filtering computationally preferable?
Frequency-domain filtering is computationally preferable to spatial-domain convolution when the **spatial filter kernel is large**.

* **Spatial Convolution** complexity: $O(N^2 M^2)$ (for an $N \times N$ image and $M \times M$ kernel).
* **Frequency Filtering** complexity: $O(N^2 \log(N^2))$ (dominated by the $\text{FFT}$ and $\text{IFFT}$).

For **small kernels**, spatial convolution is usually faster due to less $\text{FFT}$ overhead. However, as the kernel size ($M$) increases, its $M^2$ factor quickly makes spatial convolution slower than the $\text{FFT}$-based method.
