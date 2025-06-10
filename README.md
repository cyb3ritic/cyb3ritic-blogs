# Cyb3ritic's Blog

[![Live Site](https://img.shields.io/badge/Live-blog.samipshah.com.np-blue?style=for-the-badge)](https://blog.samipshah.com.np)
[![Built with Astro](https://img.shields.io/badge/Built%20with-Astro-ff5d01?style=for-the-badge&logo=astro)](https://astro.build)
[![Powered by Fuwari](https://img.shields.io/badge/Template-Fuwari-purple?style=for-the-badge)](https://github.com/saicaca/fuwari)

> A modern, elegant blog focused on penetration testing, ethical hacking, and CTF writeups. Built with the beautiful Fuwari template and powered by Astro.

## 🌐 Live Site

Visit the blog at: **[blog.samipshah.com.np](https://blog.samipshah.com.np)**

## ✨ Features

### 🎨 **Design & UI**
- Beautiful, clean design based on the Fuwari template
- Responsive layout that works on all devices
- Dark/light mode toggle with system preference detection
- Smooth animations and transitions
- Typography optimized for readability

### 🚀 **Performance**
- Built with Astro for lightning-fast static site generation
- Optimized images and assets
- Minimal JavaScript bundle
- SEO-friendly with proper meta tags

### 📝 **Content Features**
- Full-text search powered by Pagefind
- Syntax highlighting for code blocks (perfect for CTF writeups)
- KaTeX support for mathematical equations
- Auto-generated table of contents
- Automatic heading anchor links
- Tag and category system for easy navigation

### 🌍 **Advanced Features**
- RSS feed generation
- Internationalization (i18n) support
- GitHub repository integration
- Social media sharing
- Reading time estimation

## 🛠️ Tech Stack

- **Framework**: [Astro](https://astro.build) - Static site generator
- **Styling**: [Tailwind CSS](https://tailwindcss.com) - Utility-first CSS framework
- **Template**: [Fuwari](https://github.com/saicaca/fuwari) - Modern blog template
- **Search**: [Pagefind](https://pagefind.app) - Static search library
- **Icons**: [Lucide](https://lucide.dev) - Beautiful icon set
- **Deployment**: Static hosting

## 🚀 Getting Started

### Prerequisites

- **Node.js** (v18 or later)
- **pnpm** (recommended) or npm/yarn

### Installation

```bash
# Clone the repository
git clone https://github.com/cyb3ritic/cyb3ritic-blogs.git
cd cyb3ritic-blogs

# Install dependencies
pnpm install
```

### Development

```bash
# Start the development server
pnpm dev

# The site will be available at http://localhost:4321
```

### Creating Content

```bash
# Create a new blog post
pnpm new-post -- "my-awesome-ctf-writeup"

# This creates a new markdown file in src/content/posts/
```

### Building for Production

```bash
# Build the static site
pnpm build

# Preview the production build locally
pnpm preview
```

## 📁 Project Structure

```
cyb3ritic-blogs/
├── public/                 # Static assets (images, favicon, etc.)
├── src/
│   ├── components/        # Reusable UI components
│   ├── content/          # Blog posts and content
│   │   ├── posts/        # Blog post markdown files
│   │   └── config.ts     # Content collection configuration
│   ├── layouts/          # Page layout templates
│   ├── pages/            # Astro pages and API routes
│   ├── styles/           # Global CSS styles
│   ├── types/            # TypeScript type definitions
│   └── utils/            # Utility functions
├── astro.config.mjs      # Astro configuration
├── tailwind.config.cjs   # Tailwind CSS configuration
└── package.json          # Project dependencies
```

### Content Types

The blog supports various content types:
- **Blog Posts**: Technical articles, CTF writeups, tutorials
- **Tags**: Categorize posts (e.g., "CTF", "Web Security", "Network Pentest")
- **Series**: Group related posts together


## 🌐 Deployment

The blog is automatically deployed to [blog.samipshah.com.np](https://blog.samipshah.com.np) using:

1. Static site generation with `pnpm build`
2. Deployment to your preferred hosting platform
3. Custom domain configuration

## 🤝 Contributing

Found a bug or have a suggestion? Feel free to:

1. Open an issue
2. Submit a pull request
3. Contact me directly

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 About

**cyb3ritic** - Penetration Testing and Ethical Hacking Enthusiast

- 🎯 Specializing in web application security and CTF challenges
- 🚩 Active CTF player with team bi0sblr
- 📚 Sharing knowledge through detailed writeups and tutorials

### Connect with Me

- 🌐 **Blog**: [blog.samipshah.com.np](https://blog.samipshah.com.np)
- 💼 **LinkedIn**: [Your LinkedIn Profile]
- 🐙 **GitHub**: [@cyb3ritic](https://github.com/cyb3ritic)
- 🐦 **Twitter**: [Your Twitter Handle]

---

<div align="center">

**Built with ❤️ using Astro and the Fuwari template**

*Sharing knowledge, one writeup at a time* 🔐

</div>