# [Alembic](https://alembic.darn.es/)
[![Gem Version](https://badge.fury.io/rb/alembic-jekyll-theme.svg)](https://badge.fury.io/rb/alembic-jekyll-theme)

⚗ A Jekyll boilerplate theme designed to be a starting point for any Jekyll website.

![Screenshot](https://raw.githubusercontent.com/daviddarnes/alembic/master/screenshot.png)

## Contents
- [Motivation](#motivation)
- [Approach](#approach)
- [Implementation](#implementation)
- [Results](#results)
- [Difficulties](#difficulties)
- [Features](#features)
- [Examples](#examples)
- [Installation](#installation)
- [Customising](#customising)
- [Configuration](#configuration)
  - [Gem dependency settings](#gem-dependency-settings)
  - [Site settings](#site-settings)
  - [Site performance settings](#site-performance-settings)
  - [Site navigation](#site-navigation)
  - [Custom fonts](#custom-fonts)
- [Using includes](#using-includes)
- [Page layouts](#page-layouts)
- [Page and Post options](#page-and-post-options)
- [Credits](#credits)

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

to be added

## Features

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
- **Contact form** built in using [Formspree](https://formspree.io/) or [Netlify Forms](https://www.netlify.com/features/#forms)
- Designed with **[Siteleaf](http://www.siteleaf.com/)** in mind
- Has 9 of the most popular networks as performant sharing buttons
- Has documentation

## Examples

Here are a few examples of Alembic out in the wild being used in a variety of ways:

- [billmei.net](https://billmei.net/)
- [bawejakunal.github.io](https://bawejakunal.github.io/)
- [case2111.github.io](https://case2111.github.io/)
- [karateca.org](https://www.karateca.org/)

## Installation

### Quick setup

To give you a running start I've put together some starter kits that you can download, fork or even deploy immediately:

- Vanilla Jekyll starter kit:
  [![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/daviddarnes/alembic-kit)
- Forestry starter kit:
  [![Deploy to Forestry](https://assets.forestry.io/import-to-forestry.svg)](https://app.forestry.io/quick-start?repo=daviddarnes/alembic-forestry-kit&engine=jekyll)
  [![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/daviddarnes/alembic-forestry-kit)
- Netlify CMS starter kit:
  [![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/daviddarnes/alembic-netlifycms-kit&stack=cms)

- GitHub Pages with remote theme kit - **[Download kit](https://github.com/daviddarnes/alembic-kit/archive/remote-theme.zip)**
- Stackbit starter kit:
  [![Create with Stackbit](https://assets.stackbit.com/badge/create-with-stackbit.svg)](https://app.stackbit.com/create?theme=https://github.com/daviddarnes/alembic-stackbit-kit)

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

## Configuration

There are a number of optional settings for you to configure. Use the example [`_config.yml`](https://github.com/daviddarnes/alembic/blob/master/_config.yml) file in the repo and use the documentation below to configure your site:

### Gem dependency settings

`twitter`, `author` and `social` values will need to be changed to the projects' social information or removed. Look for the `Gem settings` comment within the `/_config.yml` file. These values are for the [jekyll-seo-tag](https://github.com/jekyll/jekyll-seo-tag) - follow the link to find out more.

### Site settings

You'll need to change the `description`, `title` and `url` to match with the project. You'll also need to replace the logos, default social and default offline images in the `/assets/` directory with your own graphics. Setting the site language can be done with `lang`, the theme will default to `en-US`. The `email` needs to be changed to the email you want to receive contact form enquires with. The `disqus` value can be changed to your project username on [Disqus](https://disqus.com), remove this from the `/_config.yml` file if you don't want comments enabled. Look for the `Site settings` comment within the `/_config.yml` file. The `repo` setting is optional, for now, and can be removed entirely, if you wish.

Google Analytics can be enabled via the site configuration too. Add your tracking ID to the `/_config.yml` file in the following method: `google_analytics: 'UA-XXXXXXXX-1'`. By default all IPs of site visitors are anonymous to maintain a level of privacy for the audience. If you wish to turn this off set the `google_analytics_anonymize_ip` key to `false`.

Date format can be customised in the `/_config.yml` with the option `date_format` (please refer to Liquid date filters documentation for learning about formatting possibilities). Only placeholder formatting is supported, do not try to use ordinal dates introduced in Jekyll 3.8.

The `short_name` option within `/_config.yml` is to add a custom name to the site's web application counterpart. When the website is added to a device this name will appear alonside the app icon. The short name will default to the site title if this isn't set.

### Site performance settings

Alembic comes with a couple of options to enhance the speed and overall performance of the site you build upon it.

By default the built in Service Worker is enabled, and will work on a 'network first' method. Meaning if there's no internet connection the content the Service Worker has cached will be used until the connection comes back. It will always look for a live version of the code first. To disable the Service Worker add an option called `service_worker` with a value of `false` in the `/_config.yml` file.

Another option to speed up Alembic is to enable inline CSS, which is off by default. You can enable this by setting `css_inline: true` inside your `/_config.yml` file. By switching to inline styles you bypass the use `/assets/styles.scss`, any custom styles will need to be added in `/_includes/site-styles.html` or in a new custom file.

Please note that these options aren't a "silver bullet" for making your site faster, make sure to audit and debug your site to get the best performance for your situation.

### Site navigation

There are a total of 4 different navigation types:

- `navigation_header`: The links shown in the header (it is also used on the 404 page)
- `navigation_footer`: The links shown in the footer
- `social_links`: The social icon links that are shown in the sidebar
- `sharing_links`: The social sharing buttons that are shown at the bottom of blog posts

All navigations can be edited using the `_config.yml` file. To see example usage either look for the `Site navigation` comment within the `/_config.yml` file or see [the nav-share.html include](#nav-sharehtml).

If there are no items for the `navigation_header` or `navigation_footer`, they will fallback to a list of pages within the site. The `social_navigation` properties should either be one that is already in the list (so `Twitter` or `Facebook`) or a regular `link`, this is so an icon can be set for the link.

### Custom fonts

Alembic comes with custom fonts served from Google fonts. By default it requests Merriweather but this can be any font from any provider assuming it supports requesting fonts in the same manner and does not require javascript.

This can be configured under the `custom_fonts` key.

- `urls`: The urls supplied to you from your font provider (eg https://fonts.googleapis.com/css2?family=Merriweather). For best performance try to use as few urls as possible
- `preconnect`: (optional) If your font provider serves the font files from another domain it can be useful to make a connection to that domain in advance. For example google load the font files from fonts.gstatic.com. This can be omitted if not required

If you want to customise this further you can find the include for custom fonts in `_includes/site-custom-fonts.html`.

## Using includes

There are 2 main types of includes: ones designed for the site and ones that are designed as shortcodes. Here are a list of the shortcode includes:

### `button.html`
A button that can link to a page of any kind.

Example usage: `{% include button.html text="I'm a button" link="https://david.darn.es" %}`

Available options:
- `text`: The text of the button _required_
- `link`: The link that the button goes to _required_
- `icon`: The icon that is added to the end of the button text
- `color`: The colour of the button

### `figure.html`
An image with optional caption.

Example usage: `{% include figure.html image="/uploads/feature-image.jpg" caption="Check out my photo" %}`

Available options:
- `image`: The image shown _required_
- `caption`: A caption to explain the image
- `position`: The position of the image; `left`, `right` or `center`
- `width` & `height`: Optional width and height attributes of the containing image

### `icon.html`
An icon.

Example usage: `{% include icon.html id="twitter" %}`

Available options:
- `id`: The reference for the icon _required_
- `title`: The accessible label for the icon
- `color`: The desired colour of the icon
- `width` & `height`: Width and height attributes for the icon, default is `16`

### `nav-share.html`
A set of buttons that share the current page to various social networks, which is controlled within the `_config.yml` file under the `sharing_links` keyword.

Example usage: `{% include nav-share.html %}`

Available options:
``` yml
Twitter: "#1DA1F2"
facebook: "#3B5998"
Pinterest: "#BD081C"
LinkedIn: "#0077B5"
tumblr: "#36465D"
Reddit: "#FF4500"
HackerNews: "#ff6600"
DesignerNews: "#2D72D9"
Email: true
```

_The first item is the name of the network (must be one of the ones stated above) and the second is the colour of the button. To remove a button remove the line of the same name._

### `video.html`
A YouTube video.

Example usage: `{% include video.html id="zrkcGL5H3MU" %}`

Available options:
- `id`: The YouTube ID for the video _required_

### `map.html`
A Google map. _See Google [My Maps](https://www.google.com/mymaps)_

Example usage: `{% include map.html id="1UT-2Z-Vg_MG_TrS5X2p8SthsJhc" %}`

Available options:
- `id`: The map ID for the video _required_

### `site-form.html`
Adds a contact form to the page. This can be used with [Formspree](https://formspree.io/) or [Netlify Forms](https://www.netlify.com/docs/form-handling/) depending on your setup.

Example usage: `{% include site-form.html %}`

Available options:
- `netlify_form=true`: Set whether you would like to use Netlify Forms, otherwise the form will default to Formspree
- `name`: Give the form a name, by default the form is called "Contact". The name will be reflected when form submissions come through in Netlify or in your email client. The name is also used in the label and input elements for accessibility


Use the `email` option in the `/_config.yml` to change to the desired email.

### `site-search.html`
Adds a search form to the page.

Example usage: `{% include site-search.html %}`

This include has no options. This include will add a block of javascript to the page and javascript reference in order for the search field to work correctly.

### `site-before-start.html` & `site-before-end.html`
Optional html includes for adding scripts, css, js or any embed code you wish to add to every page without the need to overwrite the entire `default.html` template.

**Example usage:** These are different to other includes as they are designed to be overwritten. If you create a `site-before-start.html` file in the `_includes/` the contents of the file will be included immediately before the closing `</head>` tag. If you create a `site-before-end.html` file the contents of the file will be included immediately before the closing `</body>` tag.

## Page layouts

As well as `page`, `post`, `blog`, there are a few alternative layouts that can be used on pages:

- `categories`: Shows all posts grouped by category, with an index of categories in a left hand sidebar
- `search`: Adds a search field to the page as well as a simplified version of the sidebar to allow more focus on the search results

## Page and Post options

There are some more specific options you can apply when creating a page or a post:

- `aside: true`: Adds a sidebar to the page or post, this is false by default and will not appear
- `comments: false`: Turns off comments for that post
- `feature_image: "/uploads/feature-image.jpg"`: Adds a full width feature image at the top of the page
- `feature_text: "Example text"`: Adds text to the top of the page as a full width feature with solid colour; supports markdown. This can be used in conjunction with the `feature_image` option to create a feature image with text over it
- `indexing: false`: Adds a `noindex` meta element to the `<head>` to stop crawler bots from indexing the page, used on the 404 page

> **Note:** The Post List Page options are actually in the collection data within the `_config.yml` file.

## Credits

- Thanks to [Simple Icons](https://simpleicons.org/) for providing the brand icons, by [Dan Leech](https://twitter.com/bathtype)
- Thanks to [Sassline](https://sassline.com/) for the typographic basis, by [Jake Giltsoff](https://twitter.com/jakegiltsoff)
- Thanks to [Flexbox mixin](https://github.com/mastastealth/sass-flex-mixin) by [Brian Franco](https://twitter.com/brianfranco)
- Thanks to [Normalize](https://necolas.github.io/normalize.css/) by [Nicolas Gallagher](https://twitter.com/necolas) and [Jonathan Neal](https://twitter.com/jon_neal).
- Thanks to [pygments-css](http://richleland.github.io/pygments-css/) for the autumn syntax highlighting, by [Rich Leland](https://twitter.com/richleland)
