> * 原文地址：[Animating URLs with Javascript and Emojis](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/)
> * 原文作者：Matthew Rayfield
> * 译者：[Wpeach](https://github.com/Wpeach/Blog)

你可以在地址栏使用 emoji（和其它图形 unicode 字符），这看着很棒，但是好像没人这么做，为什么呢？也许 emoji 对于正常的网络平台来说太过异国情调了？或许是他们因为害怕不利于SEO？

不管什么原因，维恩图上的合理性观点“没人这么做，但这是可能的”是让我兴奋的点。所以我决定花费一些时间研究在地址栏中图形字符的可能性，特别是通过 JavaScript 给这些字符加上动画。

### 循环动画

首先，确保你页面的 JavaScript 代码是 UTF-8 编码，否则无法在你的代码中显示 emoji，这可以通过设置 HTTP 头部或页面的 META 标签来实现。你很可能不用担心这个，但你可以在这里找到更多信息：[Unicode in Javascript by Flavio](https://flaviocopes.com/javascript-unicode/)。

为了达到我们想要的效果，让 emoji 像小仙女一样在地址栏里偏偏起舞，我们需要一个循环，实际上，我们所需要的只是一个循环，我们启动这个循环，它不断循环，我们的目的就达到了。这是我们的第一个循环动画，一个旋转的emoji 月亮。我猜当他们添加这个 emoji 序列时，也有这个想法吧？

![moon.gif](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/moon.gif)

```js
    var f = ['🌑', '🌒', '🌓', '🌔', '🌝', '🌖', '🌗', '🌘'];

    function loop() {
        location.hash = f[Math.floor((Date.now()/100)%f.length)];

        setTimeout(loop, 50);
    }

    loop();
```

运行代码，你可以在地址栏看到此循环的结果。

如果你不喜欢旋转的月亮，你可以选择任何你喜欢的 emoji 来替换这个数组，比如一个时钟：

![clock.gif](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/clock.gif)

```js
var f = ['🕐','🕑','🕒','🕓','🕔','🕕','🕖','🕗','🕘','🕙','🕚','🕛'];
```

这是一个非常简单的例子，真的非常简单，所以我们来升级一下循环，让它显示一串 emoji ! 这次我们使用 emoji 的`skin tone modifiers`肤色调节属性来制作一些变色宝宝：

![babies2.gif](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/babies2.gif)

```js
    var e = ['🏻', '🏼', '🏽', '🏾', '🏿'];

    function loop() {
        var s = '',
            i, m;

        for (i = 0; i < 10; i ++) {
            m = Math.floor(e.length * ((Math.sin((Date.now()/100) + i)+1)/2));
            s += '👶' + e[m];
        }

        location.hash = s;

        setTimeout(loop, 50);
    }

    loop(); 
```

我们可以使用时间和位置控制的正弦波来选择我们想要的颜色，这给了我们一个很好的颜色变幻效果！

比如我们再来一次月亮旋转，使它展开，制作一个类似于加载条的动画？好的，开始实现：

![moons.gif](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/moons.gif)

```js
    var f = ['🌑', '🌘', '🌗', '🌖', '🌕', '🌔', '🌓', '🌒'],
        d = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
        m = 0;

    function loop() {
        var s = '', x = 0;

        if (!m) {
            while (d[x] == 4) {
                x ++;
            }

            if (x >= d.length) m = 1;
            else {
                d[x] ++;
            }
        }
        else {
            while (d[x] == 0) {
                x ++;
            }

            if (x >= d.length) m = 0;
            else {
                d[x] ++;

                if (d[x] == 8) d[x] = 0;
            }
        }

        d.forEach(function (n) {
            s += f[n];
        });

        location.hash = s;

        setTimeout(loop, 50);
    }

    loop();
```

### 探索其它字符

不止是 emoji 给我们提供了一种在地址栏显示图形的方法，我们的目标中也有一些 unicode 字符。

特别有趣的是 [框线字符](https://en.wikipedia.org/wiki/Box-drawing_character):

![box-characters.png](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/box-characters.png)

它们中很多更适合二维输出，但它们在一维输出也很棒，例如，我们可以创建一个多个高度变化的块字符串，并构造一个漂亮的小波浪动画：

![wavy.gif](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/wavy.gif)

```js
    function loop() {
        var i, n, s = '';

        for (i = 0; i < 10; i++) {
            n = Math.floor(Math.sin((Date.now()/200) + (i/2)) * 4) + 4;

            s += String.fromCharCode(0x2581 + n);
        }

        window.location.hash = s;

        setTimeout(loop, 50);
    }

    loop();
```

我非常喜欢它的效果，我把它永久放在了 [wavyurl.com](http://wavyurl.com/) 上。

使用可变宽度字符，我们甚至在水平方向上摆动，创建类似于进度条的东西：

![progress.gif](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/progress.gif)

```js
    function loop() {
        var s = '',
            p;

        p = Math.floor(((Math.sin(Date.now()/300)+1)/2) * 100);

        while (p >= 8) {
            s += '█';
            p -= 8;
        }
        s += ['⠀','▏','▎','▍','▌','▋','▊','▉'][p];

        location.hash = s;
        setTimeout(loop, 50);
    }
```

进度条？这看起来，还是有用的，这让我想到了……

### 在地址栏显示视频进度

为了增加我们小实验的可能性，我提出了在地址栏中显示网络视频进度的想法。我只需附加一个函数，将我们的进度字符串定义在视频的`timeupdate`事件中，瞧！地址栏中的视频进度条包含时间和持续时间！

![video-progress.gif](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/video-progress.gif)

```js
    var video;

    function formatTime(seconds) {
        var minutes = Math.floor(seconds/60),
            seconds = Math.floor(seconds - (minutes*60));

        return ('0'+minutes).substr(-2) + ':' + ('0'+seconds).substr(-2);
    }

    function renderProgressBar() {
        var s = '',
            l = 15,
            p = Math.floor(video.currentTime / video.duration * (l-1)),
            i;

        for (i = 0; i < l; i ++) {
            if (i == p) s +='◯';
            else if (i < p) s += '─';
            else s += '┄';
        }

        location.hash = '╭'+s+'╮'+formatTime(video.currentTime)+'╱'+formatTime(video.duration);
    }

    video = document.getElementById('video');
    video.addEventListener('timeupdate', renderProgressBar);
```

我比较喜欢这个线条和圆组成的进度条，如果你喜欢别的 emoji 比如月亮，我也能让你满意：

![video-moons.gif](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/video-moons.gif)

```js
    var e = ['🌑', '🌘', '🌗', '🌖', '🌕'],
        video;

    function formatTime(seconds) {
        var minutes = Math.floor(seconds/60),
            seconds = Math.floor(seconds - (minutes*60));

        return ('0'+minutes).substr(-2) + ':' + ('0'+seconds).substr(-2);
    }

    function renderProgressBar() {
        var s = '',
            c = 0,
            l = 10,
            p = Math.floor(video.currentTime / video.duration * ((l*5)-1)),
            i;

        while (p >= 5) {
            s += e[4];
            c ++;
            p -= 5;
        }
        s += e[p];
        c ++;

        while (c < l) {
            s += e[0];
            c ++;
        }

        location.hash = s+formatTime(video.currentTime)+'╱'+formatTime(video.duration);
    }

    video = document.getElementById('video');
    video.addEventListener('timeupdate', renderProgressBar);
```

好的，将此进度条称为“有用”的延伸。 只瞄一眼，我也可以看到在视频分享 URL 中的进度。 与YouTube一样，你可以选择在特定时间创建指向视频的链接，添加视觉指示是不是很酷？嗯？

也许我还没有提出一些更有用的“技术”实现，我会继续思考这个问题。 嘿，也许你可以尝试一些东西？

### 最后

你可能想知道为什么我使用`location.hash =`，而不是新且酷的`HTML5 History API`。 有两个原因：

第一个问题是 `History API`有一个特点：它实际上更改了整个 URL 路径，而不仅仅是 `hash`。 因此，如果我使用 `History API ` 并将页面更改为`/🌑🌘🌗🌖🌕`，它看起来会比添加 ＃ 更好。 但这也意味着我的 Web 服务器必须能够响应`/🌑🌘🌗🌖🌕`，否则如果用户刷新或以其他方式导航到修改后的 URL 将会失败。 这是可行的，但比使用`location.hash =`更复杂，需要我修改服务器配置。

第二个问题有些出乎意料。 实际上，在我测试的3个浏览器中，有2个历史API被限制的。 如果我以极快的速度将我的波形网址推送到地址栏，我最终会在 Chrome 中收到以下错误：

> Throttling history state changes to prevent the browser from hanging.

Safari 给我们提供了更详细的信息：

> SecurityError: Attempt to use history.pushState() more than 100 times per 30.000000 seconds

现在，如果让我保持在这个限制下也行，但是每秒3帧只是不会影响我目前的动画效果。

好孩子 Firefox 似乎并不在乎我推送新历史的次数和速度，这真是想得太周到了。但是，两个主要的浏览器受到影响，加上需要web服务器配置来修复第一个问题，使我更愿意忍受 URL 中的 #。

### 结语

我就讲到这里。但我要告诉你，我还有一些想法，让小游戏显示在地址栏。特别是考虑到[盲文字符](https://en.wikipedia.org/wiki/Braille_Patterns)我们还没有探索，所以请继续关注。

如果你有任何问题、评论，或者只是想了解我的最新进展，请在 Twitter 上关注我: `@MatthewRayfield`。或者点[这里](https://matthewrayfield.us14.list-manage.com/subscribe?u=79fb3e3e1b6ad587be0a01fc9&id=10494371c3)订阅我的几乎从未被打扰过的电子邮件。

哦，如果你想要以上示例的源代码，[点这下载](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/animated-urls-source-code.zip)。
