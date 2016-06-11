# K-means Encoder Examples
The code in this directory contains some examples of auto-encoders based on k-means clustering.

## Random Points
The `clustering.r` script generates 15 dimensional data points that are actually on a 3-dimensional curved manifold (surface-ish). The way these points are generated is by generating 3-dimensional points and sending them through a randomly generated neural network which has a single layer of hidden nodes. This neural network simultaneously distorts the original data and projects it to the final 15 dimensional representation.

Since the neural network represents a continuous function from ![R^3](images/r3-9pt.png) to ![R^15](images/r15-9pt.png), the projected points will have a simple, but obscured structure.

This process is used to generate a training data set as well as a test data set.

After the data points are generated, the training data are clustered and the resulting clusters are used to encode and reconstruct both the training data and the test data. This is done for varying numbers of clusters from 10 to 2000.

The plot in `points.pdf` shows how accurately the points are reconstructed by simply using the centroid of the nearest cluster instead of the original point. The black line shows how well the training data is reconstructed and the red shows the same for the test data. Since the training data was used to create the clusters, it isn't too surprising that the reconstruction error is lower. In particular, by the time that we have 2000 cluster, a number of these clusters will only have a single data point assigned to them resulting in zero error for that point in the training data. The test data will inevitably not contain exactly that same data point and thus will not have any points that get zero reconstruction error.

Theoretically speaking, if the data points covered some region of 15 dimensional space uniformly, the best k-means clustering of a very large number of data points is closely related to the question of how to pack spheres as tightly as possible. The number of spheres n of a particular radius r that are required to fill a unit cube goes up with ![(1/r)^d](images/1 over r^d.png) where d is the dimension of the space we are filling. In our case, we are only filling a 3-dimensional manifold so our error (roughly the radius r) should decrease with the inverse cube root of the number of cluster centroids. This rough relationship can be seen in `cube-root.pdf`. The deviation from the ideal form for large _k_ has to do with the finite number of data points we have. I don't know the cause of the slight offset from the ideal form.

## Time-series
The `time-series.r` script generates time series data instead of random points. This time series is generated by first generating two relatively low dimensional random pulses. The time series is then created by stepping forward a bit and adding one or the other pulse to the time series. If short steps are taken, then the pulses will overlap and the result will be the sum of more than one pulse. If long steps are taken, then there will be a period of time when the output falls to zero wherever neither pulse has any influence. As with the random points demo, both training and test data is generated.

The clustering of the data proceeds by generating a windowed version of the original training time series. These windows are created by taking 32 consecutive samples from the time series and multiplying by a sin^2 windowing function that allows all the windowed values to be added together again to recreate the original time series. Each window steps 16 samples from the previous window so that the windows overlap by half.

After the windows are generated, they are clustered, again using k-means to do so. These clusters are then normalized to have magnitude 1 and each time series is encoded using the clusters and then decoded using the same clusters. Since the clusters have been normalized, encoding proceeds by computing the dot product of a window with all of the cluster centers and finding the largest dot product. This largest value is remembered along with the corresponding index.

To decode the resulting index and magnitude back to a time series, the index is used to get a cluster centroid and the magnitude is used to scale the centroid. The result is added back into the final time series with an appropriate time offset. To compute the reconstruction erorr, we can simple subtract the original time series from the result we get by first encoding and then deocding and take the mean absolute value (MAV).

As with the random points, having more clusters results in lower error, but the time series appears to be somewhat simpler in that error decreases more rapidly with increasing _k_ than for the random points.

## The figures

`cube-root.pdf` - shows how closely the accuracy versus cluster count follows theoretical considerations.

`points.pdf` - shows the error versus number of clusters for training and test data in the case of randomly distributed points.

`time-series.pdf` - shows the error versus number of clusters for training and test data in the case of time series data.

`time-series-reconstruction.pdf` - shows how well the reconstructed signal (red) matches the original test signal (in black). The blue line that is offset down by 1 shows the reconstruction error.

## Running the code
To run the code and regenerate the figures, make sure you have R installed and then run the following commands:

    $ Rscript clustering.r
    $ Rscript time-series.r

These scripts should take less than a minute to run. On a mac, you can then do this to open all of the figures in `Preview`:

    $ open *.pdf

## Getting Help
The code should be well enough commented to follow, especially with reference to this README and the associated blog. If you have problems, send me a question at one of the following addresses:

    @ted_dunning
    tdunning@maprtech.com
    ted.dunning@gmail.com
