title: Comparison of MinHash and SimHash
date: 2015-09-08 11:38:18
categories: papers
tags: [MinHash, SimHash, LSH]
---
This paper is [In Defense of MinHash Over SimHash][1].In this paper, the author provide a theoretical answer that 
> MinHash virtually always outperforms SimHash when the data are binary, as common in practice such as search

## Extracts
1.
> The collision probability of MinHash is a function of resemblance similarity, while the collision probability of SimHash is a function of cosine similarity. 

The theory of Minhash is based on Probability, while SimHash is on division of high demision. Though they have same style of results, the meanings are totally different.

2.
>  Our worst case analysis can show that MinHash significantly outperm foerms SimHash in **high similarity** region. Our restricted worst case analysis by assuming $\frac{S}{z-S} \leq R \leq \frac{S}{2-S}$ shows that MinHash indeed significantly outperforms SimHash even in **low similarity** region

3.
> **Fact**: Given a family of $(S\_0, cS\_0, p\_1, p\_2)$-senstitive hash functions, one can construct a data structure for c-NN with $O(n^{\nu}log\_{\frac{1}{p\_2}}n)$ query time, where $\nu=\frac{logp\_1}{logp\_2}$
> for MinHash, $Pr(h^{min}\_{\pi}(W\_1)=h^{min}\_{\pi}(W\_2))=\frac{\left| W\_1 \cap W\_2  \right|}{\left| W\_1 \cup W\_2 \right|}=R$, so MinHash is $(R\_0, cR\_0, R\_0, cR\_0)$ sensitive family of hash function.
> for SimHash, $h^{sim}\_w(x)=sign(w^Tx)$, $Pr(h^{sim}\_{w}(x)=h^{sim}\_{w}(y))=1-\frac{\theta}{\pi}$, where $\theta = cos^{-1}(\frac{x^Ty}{||x||\_2 ||y||\_2})$, so SimHash is $(S\_0, cS\_0, (1-\frac{cos^{-1}(S\_0)}{\pi}), (1-\frac{cos^{-1}(cS\_0)}{\pi}))$ sensitive family of hash function.

## Understand
As we all know, MinHash is defined over binary vectors, while SimHash is for real-valued data.What's more, SimHash generates a single bit output whereas MinHash generates an integer value. For comparsion, the author create 1-bit MinWise Hashing, that is to check if $h^{min}\_{\pi}(W\_1)$ is odd or not.so this 1-bit MinWise Hashing can genearte a single bit as SimHash does.

The author use $(S\_0, cS\_0, p\_1, p\_2)$ sensitive families and idealized case gap analysis to analyse the difference between MinHash and SimHash.

--- 
2015.9.8 15:57 There is still some problems to be solved. For example, how the author apply SimHash, MinHash, 1-bit MinHash to mnist dataset.  

---
2015.9.8 17:41 And SimHash is very hard to apply to comparsion of images, for the radio of the similarity of image features is very small, for smaller than documents. 

---
2015.9.8 20:31 Generally, MinHash and SimHash are created to process NLP. For images, visual words are necessary. How to generate visual words? If we use SIFT descriptors, collect as much SIFT descriptors as possible, then apply K-means so that we can get K centroids, we call that visual words. To a image, 100+ sift descriptors can be got. Compare each descriptor to K visual words and choose the closeset one. So an image(a document) can be shown as several visual words(words).Then we can easily use MinHash and SimHash.

[1]:http://jmlr.csail.mit.edu/proceedings/papers/v33/shrivastava14.pdf "In Defense of MinHash Over SimHash"

