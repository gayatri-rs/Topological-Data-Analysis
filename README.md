# Topological Data Analysis

 ## Introduction
 Topological Data Analysis is a technique which uses ideas from topology to understand large datasets.
Today most of the datasets are massive and high dimensional, to visualize them is a very difficult task. TDA provides us various tools to help visualize and understand the *"shape"* of the dataset. Because topology studies the properties of shapes that are invariant under small deformations (meaning the general shape remains the same even if stretched), visualizing the topological shape is less sensitive to noise and data deformations. It looks at the *geometry* and *connectivity* of the data.

For example, if you draw a letter “A” on a rubber sheet and stretch it, the exact distances between points change, but the topological features—the two legs and the closed triangle—remain completely intact. 


When talking about high dimension, we are talking about datasets in which the number of columns or features that each sample has is huge. For example the NKI Breast Cancer Dataset (GSE2034): This dataset contains gene expression profiling data from 286 samples (or 272 tumors depending on the subset analyzed) of lymph-node-negative breast cancer. Each patient sample acts as an observation, and the expression levels across thousands of distinct genes (often narrowed down to the most varying genes, like the top 1,500) serve as the dimensions.


Problems we face with high dimensional datasets(such as millions of protein sequences, functional neuroimaging, or RNA sequencing profiles) :
1. Visualization beyond 3D : Datasets often contain hundreds or thousands of features per observation, making visualization virtually impossible beyond three dimensions.
2. Missing Values: With such datasets we often face the problem of missing values, which might be difficult to regain for statistical techniques
3. Noise and Outliers: Real-world datasets are often noisy, additionally with outliers using distance based techiques affect the distance function rather dramatically.


If we try and plot these datasets in a 3D space we get a "*point cloud*". A point cloud is a large set of discrete data points in a three-dimensional coordinate system that represents the external surface of a physical object or environment.

To plot a high-dimensional dataset as a point cloud , we cannot plot all of its dimensions directly. So we must first use a dimensionality reduction technique, such as Principal Component Analysis (PCA), to project the data down to three dimensions (3D) so it can be visualized.

It is difficult to derive insights from these point clouds. For example the 30-dimensional breast cancer dataset from sckit-learn. Malignant indicates a cancerous tumor, Benign indicates a non-cancerous tumor.

<p align="center">
<img width="300" height="300" alt="b14d9ef4-507a-4494-b0aa-32780951b008" src="https://github.com/user-attachments/assets/6f7c1bf7-ded3-48e1-8ccf-6214cd7555dd" />
</p>

---

We can further use dimensionality reduction techniques. Using PCA (Principal Component Analysis) and t-SNE (t-Distributed Stochastic Neighbor Embedding) on high-dimensional datasets helps simplify complex data, but both methods come with major drawbacks. PCA struggles to capture curved, non-linear relationships, while t-SNE is slow, distorts global distances, and cannot easily transform new data.

Disadvantages of PCA:
1. Assumes Linear Relationships: PCA is based on straight-line projections to find directions of maximum variance. If my high-dimensional data has curves or loops (like circle or torus), PCA will flatten it and miss key patterns.
2. Sensitive to Outliers: Because PCA calculates variance by squaring distances, a few extreme data points can pull the principal components in the wrong direction.
3. Scaling: If one feature is measured in thousands and another in decimals, PCA will treat the larger-scale feature as more important unless I standardize the data first.
4. Variance Does Not Equal Importance: PCA keeps directions where data spreads out the most, it looks only at how widely data spreads out (variance), completely ignoring class labels or what we are actually trying to predict. So if the dataset contains loud background noise with huge numbers, PCA will keep that noise and throw away small-scale measurements that actually distinguish one group from another.


For example let us consider we have a 5-pointed star shaped dataset and we apply PCA to it.

<img width="1136" height="489" alt="9787138a-ceaa-4e24-b5a4-7b2bbbb698bb" src="https://github.com/user-attachments/assets/cb1e6b46-52b4-410e-95ec-901ded9d62b1" />

Because the 5-pointed star is a non-linear, with multi-branches (a central core and 5 radial flares extending in different directions), a 1D linear projection crushes the entire structure onto a straight line, completely destroying the star shape and conflating distinct clusters.

---

Now t-SNE (t-Distributed Stochastic Neighbor Embedding) is a non-linear dimensionality reduction particulary for visualizing complex, high-dimensional datasets on a 2D or 3D scatter plot. It works by taking thousands of columns of data and mapping them onto a flat grid while ensuring that data points that were close neighbors in high-dimensional space stay close together on the final map. Instead of projecting data onto straight lines like PCA, t-SNE uses probabilities.

Disadvantages of t-SNE
1. Computationally Slow: Calculating pairwise similarities across thousands of dimensions is very slow and memory-heavy. On large datasets, t-SNE can take hours to run unless you compress the data with PCA first.
2. Distorts Global Structure: t-SNE focuses heavily on keeping close neighbors together, which means the empty space and distances between separate clusters are mostly meaningless. We cannot tell if two clusters are truly far apart or closely related just by looking at the plot.
3. Sensitive to Hyperparameters: Changing settings like learning rate can dramatically alter the visual layout. Poor settings can split a single group into fake sub-clusters or club unrelated data together.
4. Non-Deterministic Results: Because t-SNE starts with random placement, running the exact same code twice can produce rotated or differently shaped clusters unless you lock a random seed.
5. Cannot Transform New Data: Unlike PCA, t-SNE does not learn a reusable mathematical formula. If you collect new patient samples or test data later, you cannot project them onto an existing t-SNE map without re-running the entire algorithm from scratch.

we can work t-SNE on the same 5-pointed star 

<img width="1099" height="489" alt="bec883c8-62b9-4f0b-9d13-cb2636f01fad" src="https://github.com/user-attachments/assets/d550d959-7779-46e3-9ef4-1d20f37c2cbf" />

\
Now even though the void in the original dataset is preserved, if we want information about the flares instead of connected components t-SNE would not help. 

Consider another example where we take synthetic data of 2 concentric circles. Appling PCA will flatten the data in any irection no matter what as data is alomost equally distributed around a center. However when t-SNE is applied, because t-SNE prioritizes preserving local neighbourhood distances over global distances, it breaks the continuous outer circle into an open C-shaped arc , even though it separates the 2 circles it fails at preserving global geometry and topological continuity.


<p align="center">
<img width="850" height="370" alt="2b875665-4b4f-45ee-8720-53527dd8cf8c" src="https://github.com/user-attachments/assets/b82aaf44-b21d-4f59-98bf-547dafb70150" />
</p>

---

So now we can talk about "shape" of the data, what exactly does it mean to understand the shape of the data?

From previous examples, we saw how PCA might lose the "void" or "loops" in the data, t-SNE might lose "continuity" or "flares" of the data. Understanding and distinguishing these different features is what we mean by understanding "shape". 

**Key Features of Data "Shape"**
1. Connected Components (Clusters): Distinct, separate islands of data points.
\
Example: Two separate concentric rings, or distinct patient subgroups.
2. Loops and Voids (Holes): Empty spaces enclosed by data points, such as a 1D ring (a loop) or a 2D hollow sphere (a void).
\
Example: Periodic cycles in time-series data, like seasonal trends, form loops.
3. Flares and Branches: Long, tentacle-like extensions protruding from a central core.
\
Example: In single-cell biology, stem cells start in a central cluster and branch out into different specialized cell types.
4. Continuity : Smooth, unbroken surfaces or curves along which data gradually changes without sudden jumps.
\
Example: A continuous spectrum of disease progression.


## Topological Techniques of Dimension Reduction

Now how does Topological Data Analysis (TDA) help us in this aspect better than other dimensionality technique?

TDA does not force high-dimensional data into a flat 2D or 3D box, instead of squashing data TDA analyzes the shape directly in its native high-dimensional space and extracts mathematical summaries of its structure. TDA provides tools like Persistent Homology, Mapper, Persistence Landscapes, Topological Data Embedding. Among thses my project particularly focuses on **Mapper** (and the filter functions it uses).

### Mapper
The Mapper algorithm is a Topological Data Analysis (TDA) technique that simplifies complex, high-dimensional datasets into a clean, easy-to-read network graph (a skeleton made of nodes and edges). Unlike standard scatter plots that squash data and lose branches, Mapper preserves flares, loops, and continuous paths.

---

**Mapper Alogirthm**

INPUTS
- **Dataset (<i>X</i>):** A collection of <i>N</i> data points in high-dimensional space.
- **Filter Function / Lens (<i>f</i>):** A rule that maps each high-dimensional point to a lower-dimensional value (usually a single 1D number).
- **Number of Intervals (<i>S</i>):** How many slices to divide the output range into.
- **Overlap Percentage (<i>p</i>):** How much adjacent slices overlap with each other (typically 20% to 50%).
- **Clustering Method (<i>C</i>):** A standard clustering algorithm (such as DBSCAN or Single-Linkage Clustering).

OUTPUT
- **Topological Graph (<i>G</i>):** A network consisting of a set of Nodes (<i>V</i>) and Edges (<i>E</i>).
  
---

Step-by-Step Procedure

**1. INITIALIZE**
- Create an empty list of Nodes (<i>V</i> = ∅).
- Create an empty list of Edges (<i>E</i> = ∅).
- Create an empty list of Projected Values (<i>Z</i>).

**2. PROJECT DATA (APPLY LENS)**
- **FOR EACH** data point <i>x</i><sub><i>i</i></sub> in Dataset <i>X</i>:
  - Compute its projected value: <i>z</i><sub><i>i</i></sub> = <i>f</i>(<i>x</i><sub><i>i</i></sub>).
  - Store <i>z</i><sub><i>i</i></sub> in list <i>Z</i>.

**3. CREATE OVERLAPPING BINS**
- Find the minimum value (<i>z</i><sub>min</sub>) and maximum value (<i>z</i><sub>max</sub>) in list <i>Z</i>.
- Divide the range [<i>z</i><sub>min</sub>, <i>z</i><sub>max</sub>] into <i>S</i> overlapping intervals (bins) <i>B</i><sub>1</sub>, <i>B</i><sub>2</sub>, …, <i>B</i><sub><i>S</i></sub>, where each bin overlaps with its neighbor by <i>p</i>%.

**4. SLICE AND CLUSTER**
- **FOR EACH** interval bin <i>B</i><sub><i>k</i></sub> (from <i>k</i> = 1 to <i>S</i>):
  - **Filter:** Gather all original high-dimensional points <i>x</i><sub><i>i</i></sub> whose projected value <i>z</i><sub><i>i</i></sub> falls inside bin <i>B</i><sub><i>k</i></sub>. Call this subset <i>X</i><sub><i>k</i></sub>.
  - **IF** subset <i>X</i><sub><i>k</i></sub> is not empty:
    - Apply Clustering Method <i>C</i> directly to the high-dimensional points in <i>X</i><sub><i>k</i></sub>.
    - **FOR EACH** distinct cluster found inside <i>X</i><sub><i>k</i></sub>:
      - Create a new Node <i>v</i> representing that specific cluster of points.
      - Add Node <i>v</i> to the graph's node list <i>V</i>.

**5. CONNECT OVERLAPPING NODES**
- **FOR EACH** pair of distinct Nodes (<i>v</i><sub><i>a</i></sub>, <i>v</i><sub><i>b</i></sub>) in list <i>V</i>:
  - Check the original data points contained inside Node <i>v</i><sub><i>a</i></sub> and Node <i>v</i><sub><i>b</i></sub>.
  - **IF** Node <i>v</i><sub><i>a</i></sub> and Node <i>v</i><sub><i>b</i></sub> share at least one common data point:
    - Draw an Edge <i>e</i> connecting Node <i>v</i><sub><i>a</i></sub> and Node <i>v</i><sub><i>b</i></sub>.
    - Add Edge <i>e</i> to the graph's edge list <i>E</i>.

**6. RETURN RESULT**
- Output the final network graph <i>G</i> = (<i>V</i>, <i>E</i>).

an example showing the steps in which mapper is applied.

<img width="2500" height="1400" alt="Gemini_Generated_Image_mvr7fwmvr7fwmvr7 (1)" src="https://github.com/user-attachments/assets/83b13386-e10b-4c3a-a987-e5d16b7769a3" />

---

Now let us apply mapper to the previous examples of 5-pointed star where we apply filter function as the distance from the center of the star.

<img width="1000" height="400" alt="3978ea2a-37d6-4aad-80f2-49915b4cffff" src="https://github.com/user-attachments/assets/4f9f64f9-306f-4e5d-bd52-fd77099a2f80" />

This demonstrates how the Mapper algorithm simplifies a continuous point cloud into a compact topological summary while preserving key structural features like loops and flares.

---

Next we apply Y-axix filter to 2 concentric circles, meaning all values are projected onto the Y-axis and then the alogrithm proceeds


 <img width="1000" height="400" alt="b4aa4484-b20d-4c4a-8e8c-bd340f8364cf" src="https://github.com/user-attachments/assets/03024fdc-5775-42ef-b010-409f86354cba" />

In this image, the filter function projects the data horizontally from bottom to top onto the Y-axis:

• Top and Bottom Caps (1 Node): Near the very top (y ≈  2.5) or bottom (y ≈ -2.5) of a circle, a horizontal slice catches just one continuous cap of points, creating 1 node.

• Middle Slices (2 Nodes): Across the middle of a circle (y ≈  0), a horizontal slice cuts through the left side and the right side separately. Because those two sides are far apart in 2D space, local clustering splits them into 2 distinct nodes (one left, one right).

• Result: As the overlapping slices move from bottom to top, the left nodes chain together, the right nodes chain together, and they rejoin at the top and bottom caps—reconstructing two intact, closed loops.

 
 By connecting overlapping clusters with edges, the resulting Mapper Graph successfully retains the original data's core topology—two disconnected, nested loops—while drastically reducing the number of data points.

 ---
 
**Why Mapper is So Useful and Different?**

Traditional dimensionality reduction tools, such as Principal Component Analysis (PCA) or t-SNE attempt to force complex, high-dimensional data onto flat 2D or 3D Euclidean planes. This often crushes or tears the data’s global non-linear
geometry, creating unstructured scatterplots that distort the true topological relationships.

Furthermore, standard clustering algorithms (like K-Means) impose artificial boundaries,
assuming data points belong to mutually exclusive “discrete islands” rather than continuous
transitions. Mapper solves this by simultaneously preserving local connectivity and global shape,
producing a highly visual, connected combinatorial graph rather than a flat, unstructured
projection.

• Very lenient : We aren’t locked into one specific technique. Mapper allows
you to plug in any filter function or clustering algorithm (like DBSCAN or PCA) that
best fits our specific dataset.

• Multiresolution: By tweaking parameters (like the size of overlapping intervals), you
can zoom in and out of the data to verify if a structural pattern is a genuine feature or
just a mathematical artifact.

• PCA only captures global linear variance, ignoring non-linear geometry, t-SNE preserve local neighbourhoods but tear apart global continuous pathways, distort inter-cluster distances, and cannot natively represent branching progressions. Mapper preserves both local connectivity and global shape simultaneously by generating a connected graph rather than projecting points onto a flat 2D plane.

• Captures Flares and Branches: If our dataset has branching subgroups (such as patients diverging into different disease subtypes), Mapper cleanly renders them as distinct arms sticking out of a central hub. Preserves Loops: If our data forms a circular cycle, the overlapping slices wrap around and connect back to the start, forming a ring of nodes.

• Handles High Dimensions Effortlessly: Even if your raw dataset has 10,000 columns (such as gene expression data), Mapper compresses thousands of points into a simple graph with a few dozen nodes.


---

Now as mesntioned above we can use _any_ filter function, so let us try using different fucntion on 2 concentric circles. We will apply centrality filter, i.e distance from the center. 

<img width="1389" height="613" alt="028d001f-8986-4960-a80f-5e8dc5637c24" src="https://github.com/user-attachments/assets/892c6f42-f8d5-4415-9daa-da3700b6904d" />
understand that after appltying the centrality filter, the filtered values will lie in small intervals around the radius of the circles and these intervals won't have any intersection as the radius of the circles are differ by a sinifncant value. so resulting graph are 2 separated nodes without any edge between them.

In the image where the filter function measures distance from the center (radius):

• Inner Circle : Every point on the inner circle sits at a radius of roughly 1.0 (<i>r<sub>1</sub></i>). When Mapper filters the data values, the entire inner circle falls into a single small neighborhood around 1.0 (<i>r<sub>1</sub></i>) and clusters into one single node (blue).

• Outer Circle : Every point on the outer circle sits at a radius of roughly 2.5 (<i>r<sub>2</sub></i>). The entire outer circle falls into a single small neighborhood around 2.5 (<i>r<sub>2</sub></i>) and clusters into one single node (orange).

• Result: Because the lens is completely blind to angles around the circle, it compresses each ring into a single dot. It accurately tells you there are 2 separate components, but it hides the fact that they are loops.

---

Note that we observed something similar in case where we applied t-SNE and got 2 separated components but one was C-shaped. 
Here also we ontain 2 separated components. 


**Does this mean the algorithm is faulty?**

The dramatic difference happens because Mapper acts like a camera, and the filter (lens) function can be thought of as literal filters that extract different features. Depending on which lens you choose, Mapper highlights different topological features of the exact same data.

### Filters
So the next question would be how to tell which filters extract what features? 

To understand which filter extracts which feature, we need to look at how the filter value changes as we move across our data. When Mapper slices data, it groups points with the same filter value together. This creates a simple rule of thumb: a filter preserves features that move across its slices, and collapses features that stay inside a single slice.



