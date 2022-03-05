# spectrograms using Librosa
Exploring how to make spectrograms with Librosa and how to save and load them. Saving data only (so without axes etc) for machine learning purposes.
 
When saving a matplotlib figure, the entire axes is saved. 

![spec1_with_axes](https://user-images.githubusercontent.com/9972594/156888701-a8fe33ea-b8d8-4c02-9ebe-c644281ce854.png)


One can use plt.axis('off'), like this: 

```
img = librosa.display.specshow(bark_spectrogram)
plt.axis('off')
plt.imsave(output_path, img, format="png")
```

However, then normalization and scaling before saving may be difficult as `img` is not an image or ndarray but a colormesh


Aother solution is to do the saving after calculating the spectrogram but _before_ displaying it, using 'skimage.io' for example:

```
#calculation
mag_spectrum, _ = librosa.core.spectrum._spectrogram(y=data, n_fft=N_FFT, hop_length=HOP)
mag_spectrum = librosa.amplitude_to_db(mag_spectrum, ref=np.max)

#save to image
def scale_minmax(X, min=0.0, max=1.0):
    X_std = (X - X.min()) / (X.max() - X.min())
    X_scaled = X_std * (max - min) + min
    return X_scaled

# min-max scale to fit inside 8-bit range
img = scale_minmax(mag_spectrum, 0, 255).astype(np.uint8)
img = np.flip(img, axis=0) # put low frequencies at the bottom in image
output_path = './spec2.png'
skimage.io.imsave(output_path, img)
```

The saved image looks like this (gray scale turns whiter as value increases):

![spec1](https://user-images.githubusercontent.com/9972594/156889978-be0af6d0-7029-4836-995c-0c4344ef38a6.png)

With reversed color scale: 

<img width="145" alt="Spec1_neg" src="https://user-images.githubusercontent.com/9972594/156890180-2fa140e2-27e3-4dd4-aa49-fff440892f7e.png">


