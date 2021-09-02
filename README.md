# Hugo and Tailwind Integration

Hugo is a static site generator created using Golang. Tailwind is a CSS solution that uses a utility-first approach. I find these two technologies interesting because of their technical elegance. Hugo has already driven my website development and operation costs down. I reckon Tailwind will provide more savings.

Here's my take on how to integrate Hugo and Tailwind.

## Technologies Used

Beginner level knowledge and experience with these technologies are required to follow the solution.

- [Hugo v0.87.0](https://github.com/gohugoio/hugo/releases)
- [TailwindCSS v2.2.9](https://www.npmjs.com/package/tailwindcss)
- [Node.js v14.17.6](https://nodejs.org/en/)
- [npm v7.21.1](https://github.com/npm/cli/releases)
  - I got this using command npm install -g npm@latest
- [VS Code](https://code.visualstudio.com/) with the following extensions:
  - PostCSS Language Support
  - Tailwind CSS IntelliSense
- [GitHub](https://github.com/) You can see the code details of files referred in my solution in this [repo](https://github.com/docdayao/hugotailwind).

## Solution

1. Create a new Hugo project - hugotailwind.

```
hugo new site hugotailwind
```

2. Edit the body section of the index.html file. We will add Tailwind utility classes to this code after we have configured Tailwind.

```
<header>
	<h1>Hello Hugo and Tailwind World!!!</h1>
</header>
<br/>
<h2>Hugo is a static site generator created using Golang.</h2>
<h2>Tailwind is a CSS solution that uses a utility-first approach.</h2>
<br/>
<h3>These are cool technologies you wouldn't want to miss.</h3>
<br/><br/>
```

3. Let's start by configuring the new Hugo project for Tailwind. Go to the project directory, run npm and install the dependencies using the default values. This will run very quickly. I edited the description and author fields of package.json.

```
npm init -y
```

4. Install Tailwind and other required dependencies - postcss, postcss-cli, postcss-import, autoprefixer, and tailwindcss. File package.json is updated with the dependencies and directory /node_modules/ is created.

```
npm install --save-dev autoprefixer@latest postcss@latest postcss-cli@latest postcss-import@latest tailwindcss@latest
```

5. Create the following config files - main.css and postcss.config.js - in directory /assets/css/.
6. Edit main.css file by adding the Tailwind directives that will generate the required Tailwind classes.

```
@import "tailwindcss/base";
@import "tailwindcss/components";
@import "tailwindcss/utilities";
```

7. Edit postcss.config.js to declare postcss-import, Tailwind, and autoprefixer as plugins.

```
module.exports = {
    plugins: [
        require('postcss-import')({
            path: ["assets/css"]
        }),
        require('tailwindcss')('assets/css/tailwind.config.js'),
        require('autoprefixer')
    ]
}
```

8. Run npx to create the tailwind.config.js config file and move the created file to /assets/css/ when done.

```
npx tailwindcss init
```

9. Declare a $styles variable in the \<head> section of index.html. Get the css file and pipe it to postCSS.

```
{{ $styles := resources.Get "css/main.css" | postCSS (dict "config" "./assets/css/postcss.config.js") }}
```

10. Code the build process after the $styles variable. Do nothing when we're building for the development environment but minify css and purge the unused classes when building for production.

```
{{ if .Site.IsServer }}

{{ else }}
	{{ $styles := $styles | minify | fingerprint | resources.PostProcess }}
{{ end }}
```

11. Add the stylesheet after the build process code.

```
<link href="{{ $styles.Permalink }}" rel="stylesheet"/>
```

12. That's it for the Tailwind configuration.
13. Now let's edit index.html by adding HTML elements with Tailwind utility classes.

```
<div class="w-full bg-red-400">
	<h1 class="text-center text-3xl font-semibold"> Here's Hugo and Tailwind working together.</h1>
</div>
<br/><br/>
<header>
 	<h1 class="text-center text-4xl font-boldtext-4xl font-bold"> Hello Hugo and Tailwind!!!</h1>
</header>
<br/>
<h2 class="text-center text-xl"> Hugo is a static site generator created using Golang...</h2>
<h2 class="text-center text-xl"> Tailwind is a CSS solution that uses a utility-first approach...</h2>
<br/>
<h3 class="text-center text-lg font-medium italic"> These are cool technologies you wouldn't want to miss!</h3>
```

14. Start the Hugo server

```
hugo server
```

15. Open your browser and point it to http://localhost:1313 and you'll see the unstyled and Tailwind styled HTML elements running in Hugo.

## References

Here's a list of references I found interesting.

1. This came out first in my search https://www.wimdeblauwe.com/blog/2021/01/18/using-hugo-with-tailwind-css-2/. It uses Hugo v0.79.0 and Tailwind 2. It didn't say the exact version of Tailwind. Jan 18, 2021
2. This is a similar approach to #1 https://praveenjuge.com/blog/install-tailwind-on-hugo/. It uses Hugo v0.83.1 and Tailwind v2.1.2. Jan 19, 2021
3. This solution was also referenced by #1 https://dev.to/jdelisle/how-to-get-tailwind-integrated-with-a-hugo-site-160o. It first came out in October 4, 2020 and edited on February 16, 2021. My solution is similar to this.
4. This solution uses Hugo Themes https://dev.to/divrhino/how-to-add-tailwindcss-to-your-hugo-site-5290. Written in a previous date but updated on April 13, 2021.
