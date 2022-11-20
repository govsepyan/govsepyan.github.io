---

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.10.2/dist/katex.min.css" integrity="sha384-yFRtMMDnQtDRO8rLpMIKrtPCD5jdktao2TV19YiZYWMDkUR5GQZR/NOVTdquEx1j" crossorigin="anonymous">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.10.2/dist/katex.min.js" integrity="sha384-9Nhn55MVVN0/4OFx7EE5kpFBPsEMZxKTCnA+4fqDmg12eCTqGi6+BB2LjY8brQxJ" crossorigin="anonymous"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.10.2/dist/contrib/auto-render.min.js" integrity="sha384-kWPLUVMOks5AQFrykwIup5lo0m3iMkkHrD0uJ4H5cjeGihAutqP0yW0J6dpFiVkI" crossorigin="anonymous" onload="renderMathInElement(document.body);"></script>
title: About Alembic
feature_text: |
  ## Alembic
  A Jekyll boilerplate theme designed to be a starting point for any Jekyll website
feature_image: "https://picsum.photos/1300/400?image=989"
excerpt: "Alembic is a starting point for [Jekyll](https://jekyllrb.com/) projects. Rather than starting from scratch, this boilerplate is designed to get the ball rolling immediately. Install it, configure it, tweak it, push it."
---

Alembic is a starting point for [Jekyll](https://jekyllrb.com/) projects. Rather than starting from scratch, this boilerplate is designed to get rolling immediately. Install it, configure it, tweak it, push it.

{% include button.html text="Fork it" icon="github" link="https://github.com/daviddarnes/alembic" color="#0366d6" %} {% include button.html text="Buy me a coffee ☕️" link="https://buymeacoffee.com/daviddarnes#support" color="#f68140" %} {% include button.html text="Tweet it" icon="twitter" link="https://twitter.com/intent/tweet/?url=https://alembic.darn.es&text=Alembic%20-%20A%20Jekyll%20boilerplate%20theme&via=DavidDarnes" color="#0d94e7" %} {% include button.html text="Install Alembic ⚗️" link="https://github.com/daviddarnes/alembic#installation" %}

## Features

- TEST TEST TEST TEST TEST
- Available as a **theme gem** and **GitHub Pages** theme
- Clear and elegant design that can be used out of the box or as solid starting point
- Tested in all major browsers, including **IE and Edge**
- Built in **Service Worker** so it can work offline and on slow connections
- **Configurable colours** and typography in a single settings file
- Extensive set of **shortcodes** to include various elements; such as buttons, icons, figure images and more
- Solid **typographic framework** from [Sassline](https://sassline.com/)
- Configurable navigation via a single file
- Modular Jekyll components
- Post category support in the form of a single post index page grouped by category
- Built in live search using JavaScript
- **Contact form** built in using [Formspree](https://formspree.io/)
- Designed with **[Siteleaf](https://www.siteleaf.com/)** in mind
- Has 9 of the most popular networks as performant sharing buttons
- Has documentation

## Examples

Here are a few examples of Alembic out in the wild being used in a variety of ways:

- [bawejakunal.github.io](https://bawejakunal.github.io/)
- [case2111.github.io](https://case2111.github.io/)
- [karateca.org](https://www.karateca.org/)


## Motivation

We wanted to create an unsupervised continual learning model that could maintain its image classifcation performance on prior datasets after being trained on new datasets. Deep learning models have achieved great performance on various tasks including image classification, detection \cite{srivastava2021comparative}, segmentation \cite{hesamian2019deep}, etc. with applications in high-impact fields such as the medical field \cite{cai2020review}. Despite this progress, most of these models still require static training, which differs from human cognition systems \cite{kudithipudi2022biological} which can train in a more dynamic manner. When introducing new data, training a network on this entire dataset -- meaning using the new and old data -- is expensive and often infeasible due to limited computation resources as well as data privacy problems; This means that requiring model training to be static is very limiting. Under the static training strategy, models need to be trained on the whole dataset at one time or else they will "forget" what they have learned abruptly upon being provided with new data; This phenomenon is termed as "catastrophic forgetting" \cite{goodfellow2013empirical,mccloskey1989catastrophic}. For example, if we train a ResNet18 \cite{he2016deep} model, termed A, on the ImageNet dataset \cite{5206848}, and we then feed it novel data with new labeled categories to get another model named B, we then find that the model B forgets the knowledge gleaned from the ImageNet dataset: i.e. the model's performance on the ImageNet dataset drops significantly.

Previous works tried to address this catastrophic forgetting problem by replaying the previous data \cite{rebuffi2017icarl, rolnick2019experience}, adding a regularization term into the loss function \cite{lee2017overcoming, kirkpatrick2017overcoming}, and isolating important parameters \cite{mallya2018piggyback, mallya2018packnet}. The above methods all aim to prevent catastrophic forgetting during supervised learning. In our project, we consider a more strict situation in which we want to achieve continual learning under unsupervised learning. Unsupervised learning is another method of training machine learning models where the model is not using labeled data. Supervised learning is useful in that it can be very accurate from less data, but the downside is that this data has to be labeled accurately, which is a notoriously tedious process that can be minimized with the use of unsupervised learning models. 

While being a useful method, supervised learning has some drawbacks. The SimSiam models \cite{chen2021exploring}, which take an unsupervised continual learning approach to their training, have demonstrated themselves to be very effective in classification tasks. SimSiam models take two randomly augmented views of the same image, process them through an encoder network, and then maximize the similarity between the outputs for the image augmentations\cite{chen2021exploring}. Even one of the most naive SimSiam models, FINETUNE, outperforms virtually every supervised continual learning model save for DER when benchmarked on Split-CIFAR-10, Split-CIFAR-100, and Split Tiny-ImageNet \cite{madaan2021representational}. In this paper, they list some metrics in which unsupervised continual learning models are able to outperform the supervised continual learning models. They demonstrate the greater performance in section 5.3 with notable findings such as: Demonstrated by higher CKA feature similarities and lower l2 distances, unsupervised continual learning models appear to be more robust to forgetting with more layers, unsupervised models are less prone to catastrophic forgetting when compared to their supervised counterparts, and the task loss has a flatter and smoother landscape.

We want to solve this problem because we would like to have a model that can learn from a variety of datasets without losing performance on previous datasets. This is important in getting closer to achieving a model that has uses outside of a very specialized use case while also minimizing manual intervention and computational costs.

## Approach

In our project, we want to mitigate catastrophic forgetting in self-supervised continual learning (SSL) using our own, novel approach; Our hope is that the deep learning models can remember what they have learned from their training on previous datasets. We are not the first to consider continual learning under unsupervised learning situations \cite{purushwalkam2022challenges, he2022unsupervised, munoz2019unsupervised}, but these attempts either utilize pseudo labels to train their models or contrast learning as a pretext task to achieve the goal. Since previous methods focus on how to achieve SSL via contrast learning, our methodology differs from them in that we use image reconstruction as a pretext task. More specifically, we will use Masked Autoencoders \cite{he2022masked} as our baseline, and we will explore whether the existing continual learning techniques are able to be compatible with it: Such as semantic drift compensation \cite{yu2020semantic}, the Nearest-Mean-of-Exemplars Classification \cite{rebuffi2017icarl}, etc. We hope that this approach will enable the training of models that can adapt to new tasks without forgetting their prior knowledge. 

Our idea for a solution to this predicament is the following: Before evaluating why the model is forgetting, we need to find out the upper bounds. To accomplish this, we need to train the whole dataset jointly with SimSiam \cite{chen2021exploring} and mask MAE \cite{he2022masked}. Following this process, we will split the various benchmarks into multiple sub-datasets; We will then split the 101 categories in the Caltech101 dataset into 10 small datasets which contain 10 or 11 classes per sub-dataset. Next, we will train the models on these ten sub-datasets sequentially, and we will evaluate their performance on the previous dataset in order to determine the level of forgetting that is taking place (e.g. one model trained on the MAE and another trained on SimSiam). We will compare their performance to conclude whether MAE is more effective in achieving continual learning. Finally, we will combine the higher-performing method with existing continual learning techniques such as semantic drift compensation \cite{yu2020semantic} and the Nearest-Mean-of-Exemplars Classification \cite{rebuffi2017icarl}.

As previously explained, SimSiam unsupervised continual learning models have shown lots of promise, so we tried testing using MAE as well with two experiments: Using the base VIT model without pretraining the model, we evaluate the average accuracy and the forgetting of the models. The following two figures show that the MAE is better at both accurately classifying (represented by the first graph below) and preventing forgetting (represented by the second graph below). 
\newpage
\vfill
\begin{figure}[!tbp]
  \label{graphs}
  \centering
  \begin{minipage}[b]{0.4\textwidth}
    \includegraphics[width=\textwidth]{accuracy_vs_numoftasks_simsiam_vs_mae.png}
    \caption{Accuracy vs Number of Tasks - SimSiam vs MAE}
  \end{minipage}
  \hfill
  \begin{minipage}[b]{0.4\textwidth}
    \includegraphics[width=\textwidth]{forgetting_simsiam_vs_mae.png}
    \caption{Forgetting of SimSiam vs MAE}
  \end{minipage}
\end{figure}

Existing approaches largely consist of contrastive learning-based or stop-gradient-based methods that can address the problem; With the goal of preventing the model from forgetting, MAE is more stable in helping the model from succumbing to pattern collapsing. Due to this capability, MAE may bring better performance in the continual learning setting. (Refer to the mathematical proof below, which demonstrates the potential increased effectiveness of using MAE)

When the MAE, $f$, falls into the collapsing, which means all inputs, $x$, are mapped into the same point, $c$:
\begin{equation}
    f(x)=c
\end{equation}
where $\forall x \in \Omega$ and $\Omega$ denotes the input images space. In this problem, the input images are masked images in the MAE setting \cite{he2022masked}. Therefore, the loss of MAE can be modeled as:
\begin{align}
    \mathcal{L}_{MAE}(f(x), y)
    &= \mathbb{E}[\|f(x)-y\|_2^2] \\
    &=\mathbb{E}^2[f(x)-y] \\
    &= \mathbb{E}^2[c-y] 
    \label{maeLoss}
\end{align}

According to the Karush–Kuhn–Tucker condi›tions (KKT) conditions, there exists a solution $c^*$ in the Equation~\ref{maeLoss}, such that:
$$2\mathbb{E}[c^*-y]=0$$
Therefore, $\mathbb{E}[c^*]=\mathbb{E}[y]$

Since our objective is $\displaystyle{\minimize_{f}\mathcal{L}_{MAE}(f(x), y)}$, so we can get the lower boundary of the $\mathcal{L}_{MAE}$:
\begin{align*}
    \mathcal{L}_{MAE}(f(x), y)
    &= \mathbb{E}[\|f(x)-y\|_2^2] \\
    &\geq\mathbb{E}^2[c^*-y] \\
    &= \mathbb{E}^2[\mathbb{E}[y]-y]\\
    &= \mathbb{E}[(\mathbb{E}[y]-y)^2]\\
    &= \sigma^2(y)
\end{align*}
where the $\sigma$ denotes the variance and the y is the ground truth (original image). If the original image has a lot of variance, the lower boundary is also high. Thus, it's not easy to fall into pattern collapsing.


Once $f$, the mask autoencoder (MAE), falls into pattern collapsing, then all inputs $x$ are mapped into the same point $c$, such that:
$f(x)=c$   where  $x$ denotes the input image space. 
	Therefore, the loss of the MAE can be represented by:
$\mathcal{L}_{MAE}(f(x), y)
    = \mathbb{E}[\|f(x)-y\|_2^2] \\$

In trying to solve this older problem with a newer method, we hope it may inspire the community in the future to build off of this.

	
We will use the CUB200 \cite{wah_branson_welinder_perona_belongie_2022}, Caltech101 \cite{li_andreeto_ranzato_perona_2022}, and Flowers102 \cite{nilsback2008automated} datasets as training benchmarks, and then we will evaluate the model trained under SimSiam and MAE.

In Fig~\ref{fig:timetable}, we list our time table. We began by creating the model and gathering training data on October 6, and we finished this phase around November 1. We will finish testing our method and achieving the relevant visualization before November 24, and we will finish the final report around December 10.

	We set up two metrics to evaluate the performance of our solution. One is used to measure the accuracy (where a larger number is better), and the other one is used to evaluate the forgetting (where a lower number is better). 
	
	Let a$\tau$, i denote the test accuracy of task i after learning task T$\tau$ using a KNN on frozen pre-trained representations on task $T_{\tau}$

Average accuracy: $A_\tau = \frac{1}{\tau}\sum_{i=1}^\tau a_{\tau,i}$

Average Forgetting: $F=\frac{1}{T-1}\sum_{i=1}^{T-1}max_{\tau\in\{1,...,T\}}(a_{\tau,i}-a_{T,i}))$



## Implementation

to be added

## Results

to be added

## Difficulties

Although the project went well overall, there were a few challenges that arose along the way. When we first started the project, we wanted to take a slightly different approach to solving our problem; We were going to use


## Installation

### Quick setup

To give you a running start I've put together some starter kits that you can download, fork or even deploy immediately:

- ⚗️🍨 Vanilla Jekyll starter kit  
  [![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/daviddarnes/alembic-kit){:style="background: none"}
- ⚗️🌲 Forestry starter kit  
  [![Deploy to Forestry](https://assets.forestry.io/import-to-forestry.svg)](https://app.forestry.io/quick-start?repo=daviddarnes/alembic-forestry-kit&engine=jekyll){:style="background: none"}  
  [![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/daviddarnes/alembic-forestry-kit){:style="background: none"}
- ⚗️💠 Netlify CMS starter kit  
  [![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/daviddarnes/alembic-netlifycms-kit&stack=cms){:style="background: none"}

- ⚗️:octocat: GitHub Pages with remote theme kit  
  {% include button.html text="Download kit" link="https://github.com/daviddarnes/alembic-kit/archive/remote-theme.zip" color="#24292e" %}
- ⚗️🚀 Stackbit starter kit  
  [![Create with Stackbit](https://assets.stackbit.com/badge/create-with-stackbit.svg)](https://app.stackbit.com/create?theme=https://github.com/daviddarnes/alembic-stackbit-kit){:style="background: none"}

### As a Jekyll theme

1. Add `gem "alembic-jekyll-theme"` to your `Gemfile` to add the theme as a dependancy
2. Run the command `bundle install` in the root of project to install the theme and its dependancies
3. Add `theme: alembic-jekyll-theme` to your `_config.yml` file to set the site theme
4. Run `bundle exec jekyll serve` to build and serve your site
5. Done! Use the [configuration](#configuration) documentation and the example [`_config.yml`](https://github.com/daviddarnes/alembic/blob/master/_config.yml) file to set things like the navigation, contact form and social sharing buttons

### As a GitHub Pages remote theme

1. Add `gem "jekyll-remote-theme"` to your `Gemfile` to add the theme as a dependancy
2. Run the command `bundle install` in the root of project to install the jekyll remote theme gem as a dependancy
3. Add `jekyll-remote-theme` to the list of `plugins` in your `_config.yml` file
4. Add `remote_theme: daviddarnes/alembic@main` to your `_config.yml` file to set the site theme
5. Run `bundle exec jekyll serve` to build and serve your site
6. Done! Use the [configuration](#configuration) documentation and the example [`_config.yml`](https://github.com/daviddarnes/alembic/blob/master/_config.yml) file to set things like the navigation, contact form and social sharing buttons

### As a Boilerplate / Fork

_(deprecated, not recommended)_

1. [Fork the repo](https://github.com/daviddarnes/alembic#fork-destination-box)
2. Replace the `Gemfile` with one stating all the gems used in your project
3. Delete the following unnecessary files/folders: `.github`, `LICENSE`, `screenshot.png`, `CNAME` and `alembic-jekyll-theme.gemspec`
4. Run the command `bundle install` in the root of project to install the jekyll remote theme gem as a dependancy
5. Run `bundle exec jekyll serve` to build and serve your site
6. Done! Use the [configuration](#configuration) documentation and the example [`_config.yml`](https://github.com/daviddarnes/alembic/blob/master/_config.yml) file to set things like the navigation, contact form and social sharing buttons

## Customising

When using Alembic as a theme means you can take advantage of the file overriding method. This allows you to overwrite any file in this theme with your own custom file, by matching the file name and path. The most common example of this would be if you want to add your own styles or change the core style settings.

To add your own styles copy the [`styles.scss`](https://github.com/daviddarnes/alembic/blob/master/assets/styles.scss) into your own project with the same file path (`assets/styles.scss`). From there you can add your own styles, you can even optionally ignore the theme styles by removing the `@import "alembic";` line.

If you're looking to set your own colours and fonts you can overwrite them by matching the variable names from the [`_settings.scss`](https://github.com/daviddarnes/alembic/blob/master/_sass/_settings.scss) file in your own `styles.scss`, make sure to state them before the `@import "alembic";` line so they take effect. The settings are a mixture of custom variables and settings from [Sassline](https://medium.com/@jakegiltsoff/sassline-v2-0-e424b2881e7e) - follow the link to find out how to configure the typographic settings.
