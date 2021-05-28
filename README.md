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

To automate this process and export as a LAS file: 

```
cd "C:\Program Files\CloudCompare"
CloudCompare -O -GLOBAL_SHIFT -519800 -5913100 0 "C:\Users\Christine\Desktop\FYBR\messyPointClouds\forestPointCloud.las" -C_EXPORT_FMT LAS -CROP2D Z 20 857.974454 448.451725 874.370857 350.409992 867.761214 190.557949 854.125220 97.602778 820.199793 64.616813 713.972274 76.104947 595.212652 180.209955 449.533599 325.580762 339.014706 323.318679 268.138876 336.172183 166.540265 404.351828 56.220159 581.983130 28.638806 681.245105 28.357809 705.289047 47.827661 719.001115 615.181086 728.572750 190.804185 700.234016 334.183391 617.489340 699.722966 476.583019 857.974454 448.451725 
```
- CROP2D crops all loaded clouds inside a given 2D polygon
- Specify orthogonal dimension that will be ignored. Since we don't need the Z coordinates, I specified that we ignore Z
- Number of vertices of the polygon given to me has 20 vertices, which is specified 
- All 20 vertices' coordinates are specified 

### 2. A "noise" filter is automated to filter out unsightly stray points that can throw off the point cloud centroid, making it hard to view online. 

After testing out two different filter options, it seems they are pretty similar. The noise filter gives more control but you have to set more parameters!

A little description of each filter: 
#### SOR filter (Tools > Clean > SOR filter)
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
- specify Number of neighbours  
- specify Sigma multiplier  

#### Noise filter (Tools > Clean > Noise filter)
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
- The radius, or spherical neighbourhood is specified (REL)
- Remove isolated points (RIP) 

### Apply Noise filter + Beauty mask + Export as LAS file (Using Sawmill Point Cloud) 
![image](https://user-images.githubusercontent.com/83466109/119951918-525cc600-bf51-11eb-8476-b86932b608b5.png)
```
cd "C:\Program Files\CloudCompare"
CloudCompare -O -GLOBAL_SHIFT -653400 -3401500 0 "C:\Users\Christine\Desktop\FYBR\messyPointClouds\SawmillPointCloud.las" -C_EXPORT_FMT LAS -NOISE RADIUS 0.4 REL 1.0 RIP -CROP2D Z 12 261.674983 492.431959 629.045639 492.864675 693.979116 475.130979 705.731322 404.261044 701.787409 83.405928 652.802746 32.880245 175.277298 23.327723 55.555478 121.100196 4.951358 189.498857 4.062920 259.814967 66.842917 366.937263 166.251288 439.070517 261.674983 492.431959
```
