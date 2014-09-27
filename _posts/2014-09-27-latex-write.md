---
layout: post
title: Latex on jekyll
category: math
---

# Latex on jekyll - Mathjax

- mathjax is a powerful tool to surport jekyll to write latex, and the way to install and use it is very easy.


- **Install**


```sh
gem install mathjax
```

- **Modify _config.yml**

```sh
markdown: kramdown
```

- **Add js to your template**

```js
    <script type=”text/x-mathjax-config”>
    MathJax.Hub.Config({
        tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}
    });
    </script>
        <script type=”text/javascript”
        src=”http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML”>
    </script>
    })
```

- **Enjoy it !**

inline formula like this:

`$E=mc^2$ is a powerful`

then, you can see:

$E=mc^2$ is a is powerful

u can also use it like this:

```sh
$$ 
\begin{aligned} 
\dot{x} &= \sigma(y-x) \\\\ 
\dot{y} &= \rho x - y - xz \\\\ 
\dot{z} &= -\beta z + xy \end{aligned} 
$$
```

then

$$ 
\begin{aligned} \dot{x} &= \sigma(y-x) \\\\ 
\dot{y} &= \rho x - y - xz \\\\
\dot{z} &= -\beta z + xy \end{aligned} 
$$

just enjoy it guys ;)
