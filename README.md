# Rohan Reddy - Personal Portfolio Website

A modern, responsive personal portfolio website built with React, TypeScript, and Express. Features GitHub integration, tech news feed, interactive code lab, contact form, and admin panel.

## 🚀 Features

### Core Features
- **Responsive Design**: Mobile-first design with dark/light theme support
- **GitHub Integration**: Automatically displays repositories and gists
- **Tech News Feed**: Real-time tech news from NewsAPI
- **Interactive Code Lab**: Monaco Editor for viewing and running code snippets
- **Contact Form**: With database storage and email notifications
- **Admin Panel**: Manage contact form submissions
- **Performance Optimized**: Caching layer for all API endpoints

### Technical Features
- **Full-Stack TypeScript**: End-to-end type safety
- **Modern Stack**: React, Tailwind CSS, Express, Drizzle ORM
- **Real-time Updates**: WebSocket support for dynamic content
- **SEO Optimized**: Meta tags, Open Graph, and structured data
- **Production Ready**: Environment configuration and error handling

## 🛠️ Tech Stack

### Frontend
- **React 18** with TypeScript
- **Tailwind CSS** for styling
- **Wouter** for routing
- **TanStack Query** for state management
- **Monaco Editor** for code display
- **Lucide React** for icons

### Backend
- **Node.js** with Express
- **TypeScript** for type safety
- **Drizzle ORM** with PostgreSQL
- **Nodemailer** for email notifications
- **In-memory caching** for performance

### APIs & Integrations
- **GitHub API** for repositories and gists
- **NewsAPI** for tech news
- **Quotable API** for daily quotes

## 📦 Installation & Setup

### Prerequisites
- Node.js 18+ 
- npm or yarn
- PostgreSQL database (optional - uses in-memory by default)

### Quick Start

1. **Clone the repository**
   ```bash
   git clone https://github.com/Rohan1011/Portfolio-Website-.git
   cd Portfolio-Website-
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Environment Configuration**
   Create a `.env` file in the root directory:
   ```env
   # Required API Keys
   NEWS_API_KEY=your_newsapi_key_here
   GITHUB_TOKEN=your_github_token_here
   SESSION_SECRET=your_session_secret_here

   # Optional Email Configuration
   SMTP_HOST=smtp.gmail.com
   SMTP_PORT=587
   SMTP_USER=your_email@gmail.com
   SMTP_PASSWORD=your_app_password
   SMTP_FROM=noreply@rohan-reddy.com
   ADMIN_EMAIL=admin@rohan-reddy.com

   # Site Configuration
   SITE_URL=https://rohan-reddy.com
   NODE_ENV=production
   ```

4. **Start the application**
   ```bash
   npm run dev
   ```

   The app will be available at `http://localhost:5000`

## 🔑 API Keys Setup

### NewsAPI Key
1. Visit [NewsAPI.org](https://newsapi.org/)
2. Sign up for a free account
3. Get your API key and add it to `.env` as `NEWS_API_KEY`

### GitHub Token
1. Go to GitHub Settings > Developer settings > Personal access tokens
2. Generate a new token with `repo` and `user` scopes
3. Add it to `.env` as `GITHUB_TOKEN`

### Email Configuration (Optional)
For Gmail SMTP:
1. Enable 2FA on your Google account
2. Generate an app password
3. Use your Gmail and app password in the SMTP configuration

## 🚀 Deployment

### CloudFlare Tunnel Deployment

1. **Install CloudFlare Tunnel**
   ```bash
   # Install cloudflared
   curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
   sudo dpkg -i cloudflared.deb
   ```

2. **Authenticate with CloudFlare**
   ```bash
   cloudflared tunnel login
   ```

3. **Create and Configure Tunnel**
   ```bash
   # Create tunnel
   cloudflared tunnel create rohan-portfolio
   
   # Create config file
   nano ~/.cloudflared/config.yml
   ```

   Add to config.yml:
   ```yaml
   tunnel: YOUR_TUNNEL_ID
   credentials-file: /home/user/.cloudflared/YOUR_TUNNEL_ID.json
   
   ingress:
     - hostname: rohan-reddy.com
       service: http://localhost:5000
     - service: http_status:404
   ```

4. **Start the tunnel**
   ```bash
   # Run tunnel
   cloudflared tunnel --config ~/.cloudflared/config.yml run rohan-portfolio
   
   # Or as a service
   sudo cloudflared --config ~/.cloudflared/config.yml service install
   sudo systemctl start cloudflared
   ```

5. **DNS Configuration**
   ```bash
   cloudflared tunnel route dns rohan-portfolio rohan-reddy.com
   ```

### Alternative Deployments
- **Vercel**: Zero-config deployment with automatic HTTPS
- **Netlify**: Full-stack deployment with serverless functions
- **Railway**: Simple deployment with PostgreSQL
- **DigitalOcean**: VPS deployment with PM2

## 📁 Project Structure

```
├── client/                 # Frontend React app
│   ├── src/
│   │   ├── components/     # Reusable UI components
│   │   ├── pages/         # Page components
│   │   ├── lib/           # Utilities and configs
│   │   └── App.tsx        # Main app component
│   └── index.html
├── server/                 # Backend Express app
│   ├── routes.ts          # API routes
│   ├── storage.ts         # Data layer
│   ├── emailService.ts    # Email notifications
│   └── index.ts           # Server entry point
├── shared/                 # Shared TypeScript types
│   └── schema.ts          # Database schemas
└── README.md
```

## 🔧 Configuration

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `NEWS_API_KEY` | Yes | NewsAPI key for tech news |
| `GITHUB_TOKEN` | Yes | GitHub personal access token |
| `SESSION_SECRET` | Yes | Session encryption key |
| `SMTP_HOST` | No | Email server hostname |
| `SMTP_USER` | No | Email username |
| `SMTP_PASSWORD` | No | Email password |
| `ADMIN_EMAIL` | No | Admin email for notifications |
| `SITE_URL` | No | Full site URL for links |

### Features Configuration

```typescript
// Enable/disable features in server/routes.ts
const FEATURES = {
  CACHING: true,           // API response caching
  EMAIL_NOTIFICATIONS: true, // Contact form emails
  GITHUB_INTEGRATION: true,  // Repo/gist display
  NEWS_FEED: true,          // Tech news
};
```

## 🎨 Customization

### Styling
- Edit `client/src/index.css` for global styles
- Modify `tailwind.config.ts` for theme customization
- Update color scheme in CSS variables

### Content
- Update personal information in `client/src/pages/Home.tsx`
- Modify project descriptions in GitHub repos
- Customize news categories in `client/src/pages/News.tsx`

## 📊 Performance Features

- **API Caching**: 15-30 minute cache for external APIs
- **Lazy Loading**: Components load on demand
- **Image Optimization**: Responsive images with proper sizing
- **Bundle Splitting**: Code splitting for faster loading
- **CDN Ready**: Static assets optimized for CDN delivery

## 🛡️ Security Features

- **Environment Variables**: Secure API key storage
- **CORS Protection**: Configured for production domains
- **XSS Protection**: Input sanitization and validation
- **Rate Limiting**: Basic protection against abuse
- **Secure Headers**: Security headers in production

## 🧪 Testing

```bash
# Run frontend tests
npm run test

# Run backend tests  
npm run test:server

# Run e2e tests
npm run test:e2e
```

## 📝 API Endpoints

### Public Endpoints
- `GET /api/github/repos` - GitHub repositories
- `GET /api/github/gists` - GitHub gists  
- `GET /api/news` - Tech news articles
- `GET /api/quote` - Daily inspirational quote
- `POST /api/contacts` - Submit contact form

### Admin Endpoints
- `GET /api/contacts` - List contact submissions
- `POST /api/cache/clear` - Clear API cache

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 About

Built by [Rohan Reddy](https://rohan-reddy.com) - Computer Science Student

- **GitHub**: [@Rohan1011](https://github.com/Rohan1011)
- **Email**: contact@rohan-reddy.com
- **Website**: [rohan-reddy.com](https://rohan-reddy.com)

## 🙏 Acknowledgments

- **NewsAPI** for tech news feed
- **GitHub API** for repository integration  
- **Quotable API** for daily quotes
- **Tailwind CSS** for styling system
- **React** and **TypeScript** communities