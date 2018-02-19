# Finding Lane Lines on the Road

## Reflection

### The pipeline

The `pipeline` is created in a similar fashion to the lesson. The steps
are in this order

- apply grayscale
- Gaussian blur
- Canny edge detection
- crop the region of interest
- Hough algorithm
- Classify, average and extrapolate lines (`draw_lines`)
- Overlay lines on original image

I did not do extensive tuning but used mostly the recommended parameters
from the lesson. For the region of interest, I selected a trapezoid
spanning from the full bottom of the image to a bit under half the
middle of the image with a width of 10 % of the image that is centered
again.

Most thoughts went into `draw_lines`. It takes the lines that have been
detected by the Hough transformation and at first translates them to
normal form, i.e. the parameters `m` and `c` of `y = m * x + c` to
describe a line. If this gives a nonhorizontal line (`|m| > 0.1`), I
classify and group the lines according to the sign of `m` into left and
right lines.

Left and right line ensembles are each averaged with `stable_mean` and
then extrapolated with `extrapolate_line`. The results are returned to
allow to average over two images in movies. In `stable_mean`, lines that
have parameters, which are further away from the average than 80 % of
the distance of maximum to minimum are disregarded. Finally, in case
it's a movie where the results of the last frame are known, the result
will be averaged with the last frame to obtain more stable lines.

The results can be seen in [test_images_output](test_images_output) as
well as [test_videos_output](test_videos_output).

### Potential shortcomings

The presented solution is not really robust. It works well on all images
and the two videos (i.e. on straight lines) but has problems in curves
as seen on the challenges example.  Also, it is not really smooth but
wiggles around quite a bit. Finally, the performance in low-light
situations is not tested.

### Possible improvements

One could try to improve the parameters of this solution. Regions for
possible fine-tuning are:

- Lower the horizon of the region of interest
- Filter out more outliers in `stable_mean`
- Average over more frames in `stable_mean`

Furthermore, one could try to apply a color selection on white and
yellow. However, to obtain a truly smooth solution, one likely needs a
smarter approach than the techniques shown in the lecture and applied in
this project.
