# RandAugment with Keypoints

Unofficial RandAugment\[1\] implementation for image and keypoint augmentation.

![](docs/assets/original-v-augment.png)

**Support for Consistency Training:** Consistency Training has been used to attain state-of-the-art results on image classification problems [2, 3]. One challenge in adapting the classification technique proposed in \[1\] to handle keypoints is that the augmentations cause the keypoints to become misaligned. In order to deal with this, we implement a `RandAugment.apply_inv_keypoints`, which takes keypoint predictions from augmented images and normalizes them so that they can be compared.

## Install

```bash
python setup.py install
```

## Basic Usage

Using RandAugment Keypoints is simple. First use `plan_augment` in order to create `(magnitude, operation, directions)` and then apply them using `apply_image` or `apply_keypoints`.

Example:

```python
import augkey

image = ... # your PIL.Image
randaug = augkey.RandAugment()
plan = randaug.plan_augment() 
augmented_image = randaug.apply_image(image, *plan)
```

See the [demo notebook](demo.ipynb) for more examples.

## Adding/Modifying Operations

By default, `RandAugment` uses the operations discussed in the original paper\[1\].

In response to discussion about the large impact of augmentations and their magnitude ranges in the original paper\[1\], this package abstracts operations to make them easier to create and modify.

```python
import augkey
from augkey import operations as ops

# Create your new operation.
class Crop(ops.Operation):
    """Crop an image"""
    def transform_image(self, image, magnitude=0, direction=1):
        value = self.magnitude_range[magnitude]
        width, height = im.size
        im.crop((value, height - value, width - value, value))

    # if using keypoints, similarly implement `transform_keypoints`.

operations = [
    Crop(np.arange(30)), # Pass the magnitude_range in as an argument.
    ops.Rotate(np.linspace(0, math.radians(30), 30)),
]

randaug = ra.RandAug(operations=operations)
```

## Contributing

### Linting

Please run pylint with a max-line-length of 80.
```pip install pylint```

### Running Tests

Tests require `opencv-python`, install with `pip install opencv-python`

To run tests: `python -m unittest -v`

## References

1. Cubuk, Ekin D., Barret Zoph, Jonathon Shlens, and Quoc V. Le. “RandAugment: Practical Automated Data Augmentation with a Reduced Search Space.” ArXiv:1909.13719 \[Cs\], November 13, 2019. [http://arxiv.org/abs/1909.13719].

2. Sohn, Kihyuk, David Berthelot, Chun-Liang Li, Zizhao Zhang, Nicholas Carlini, Ekin D. Cubuk, Alex Kurakin, Han Zhang, and Colin Raffel. “FixMatch: Simplifying Semi-Supervised Learning with Consistency and Confidence.” ArXiv:2001.07685 [Cs, Stat], November 25, 2020. [http://arxiv.org/abs/2001.07685].

3. Xie, Qizhe, Zihang Dai, Eduard Hovy, Minh-Thang Luong, and Quoc V. Le. “Unsupervised Data Augmentation for Consistency Training.” ArXiv:1904.12848 [Cs, Stat], November 5, 2020. [http://arxiv.org/abs/1904.12848].
