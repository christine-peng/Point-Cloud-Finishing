# Point-Cloud-Finishing

### This project automates the process of "beautifying" a photogrammetric point cloud for display online. 

#### 1. Apply a "beauty mask" to clip the point cloud with a polygon to remove "scrappy" looking edges. The process is as follows: 
* a.) Import polygon and point cloud into CloudCompare
![image](https://user-images.githubusercontent.com/83466109/118745973-2ed8a380-b80c-11eb-8b66-69cc5bef8238.png)

* b.) Set top view to to line up point cloud with polygon ![image](https://user-images.githubusercontent.com/83466109/118746563-50865a80-b80d-11eb-8c55-2a34c8892a34.png)
     
* c.) Use the segment tool (scissors) ![image](https://user-images.githubusercontent.com/83466109/118746162-8b3bc300-b80c-11eb-8004-8b795dad96f6.png)

* d.) Import polyline from DB for segmentation ![image](https://user-images.githubusercontent.com/83466109/118746805-bbd02c80-b80d-11eb-982b-f57bc28f8df3.png)

* e.) Segment in (red polygon) ![image](https://user-images.githubusercontent.com/83466109/118746752-a3f8a880-b80d-11eb-92e1-c65662526056.png)

* f.) Confirm segmentation ![image](https://user-images.githubusercontent.com/83466109/118746695-89263400-b80d-11eb-917b-d29097149876.png)

* g.) Save new clipped file ![image](https://user-images.githubusercontent.com/83466109/118746914-e8844400-b80d-11eb-9c11-a6a8946f527f.png)

#### 2. A "noise" filter is automated to filter out unsightly stray points that can throw off the point cloud centroid, making it hard to view online. 

After testing out two different filter options, it seems they are pretty similar. The noise filter gives more control but you have to set more parameters!

A little description of each filter: 
### SOR filter (Tools > Clean > SOR filter)
- This tool functions to compute the average distance of each point to its neighbors (considering k nearest neighbors for each k is the first parameter). It then rejects the points that are further than the average distance plus the number of standard deviations (second parameter).
https://www.cloudcompare.org/doc/wiki/index.php?title=SOR_filter
- After running through various parameters, it seems the parameter will need to be adjusted depending on how much noise is in each individual point cloud. Since the file used to test parameters (forestPointCloud.las) has a large number of sparse points, a parameter as shown below would be best suited to clean up this particular point cloud. 

Parameter: ![image](https://user-images.githubusercontent.com/83466109/118748135-284c2b00-b810-11eb-915b-e3531f089421.png)

Before: ![image](https://user-images.githubusercontent.com/83466109/118748611-dd7ee300-b810-11eb-98d5-c9cfb0939721.png)
After:![image](https://user-images.githubusercontent.com/83466109/118748669-f6879400-b810-11eb-9532-ef1fd128e19b.png)

To automate this process and export as a LAS file:
```
cd "C:\Program Files\CloudCompare"
CloudCompare -O "C:\Users\Christine\Desktop\FYBR\cleanPointClouds\forestPointCloud.las" -C_EXPORT_FMT LAS -SOR 6 1 
```

### Noise filter (Tools > Clean > Noise filter)
* This tool is similar to the SOR filter but considers the distance to the underlying surface rather than distance to neighbors. The algorithm fits locally around each point of the cloud, then removes the point if it's too far away from the point cloud. This filter is very similar to a low pass filter.
http://www.cloudcompare.org/doc/wiki/index.php?title=Noise_filter
* Parameters used: input a number of neighbors around each group of points, or specify a ball radius, and input max error (distance of the point to the fitter plane) to decide of a point is rejected or not.
* After running through various parameters, it seems the parameter will need to be adjusted depending on how sparse the point clouds are. After testing several different parameters for the forestPointCloud.las file, it seems a parameter with a default radius and absolute max error outputs the least amount of stray points.

Parameter: ![image](https://user-images.githubusercontent.com/83466109/118750234-e1603480-b813-11eb-81f6-392a3f4c82d2.png)

To automate this process and export as a LAS file: 

```
cd "C:\Program Files\CloudCompare"
CloudCompare -O "C:\Users\Christine\Desktop\FYBR\cleanPointClouds\forestPointCloud.las" -C_EXPORT_FMT LAS -NOISE RADIUS 0.4 REL 1.0 RIP 
```
