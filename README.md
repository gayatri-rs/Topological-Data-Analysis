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
<img width="300" height="300" alt="b14d9ef4-507a-4494-b0aa-32780951b008" src="https://github.com/user-attachments/assets/5bb096f1-a9ac-43e4-bd77-dc4f5da3ef3b" />
</p>

We can further use dimensionality reduction techniques. Using PCA (Principal Component Analysis) and t-SNE (t-Distributed Stochastic Neighbor Embedding) on high-dimensional datasets helps simplify complex data, but both methods come with major drawbacks. PCA struggles to capture curved, non-linear relationships, while t-SNE is slow, distorts global distances, and cannot easily transform new data.

Disadvantages of PCA:
1. Assumes Linear Relationships: PCA is based on straight-line projections to find directions of maximum variance. If my high-dimensional data has curves or loops (like circle or torus), PCA will flatten it and miss key patterns.
2. Sensitive to Outliers: Because PCA calculates variance by squaring distances, a few extreme data points can pull the principal components in the wrong direction.
3. Scaling: If one feature is measured in thousands and another in decimals, PCA will treat the larger-scale feature as more important unless I standardize the data first.
4. Variance Does Not Equal Importance: PCA keeps directions where data spreads out the most, it looks only at how widely data spreads out (variance), completely ignoring class labels or what we are actually trying to predict. So if the dataset contains loud background noise with huge numbers, PCA will keep that noise and throw away small-scale measurements that actually distinguish one group from another.


For example let us consider we have a 5-pointed star shaped dataset and we apply PCA to it.

<img width="1136" height="489" alt="9787138a-ceaa-4e24-b5a4-7b2bbbb698bb" src="https://github.com/user-attachments/assets/cb1e6b46-52b4-410e-95ec-901ded9d62b1" />

Because the 5-pointed star is a non-linear, with multi-branches (a central core and 5 radial flares extending in different directions), a 1D linear projection crushes the entire structure onto a straight line, completely destroying the star shape and conflating distinct clusters.


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


git checkout --orphan temp_branch
git add -A
git commit -m "Initial commit"
git branch -D main
git branch -m main
git push -f origin main
 
