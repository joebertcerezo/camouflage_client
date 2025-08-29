# Camouflage Client

A modern WebRTC peer-to-peer video chat application built with Nuxt 4, Vue 3, TypeScript, and Tailwind CSS. This client application provides a seamless interface for establishing direct video connections between peers using WebRTC technology.

## 🚀 Features

- **Real-time Video Communication**: Direct peer-to-peer video calls using WebRTC
- **SDP Exchange Interface**: Visual interface for creating and managing WebRTC offers and answers
- **Connection Status Monitoring**: Real-time connection status indicators with visual feedback
- **Modern UI Components**: Built with shadcn/ui and Reka UI for a polished user experience
- **Responsive Design**: Mobile-first design that works across all devices
- **TypeScript Support**: Full type safety throughout the application
- **Error Handling**: Comprehensive error handling with user-friendly error messages
- **Accessibility**: Built with accessibility best practices using ARIA labels and semantic HTML

## 🛠️ Tech Stack

### Core Framework

- **[Nuxt 4](https://nuxt.com/)** - The intuitive Vue framework
- **[Vue 3](https://vuejs.org/)** - Progressive JavaScript framework
- **[TypeScript](https://www.typescriptlang.org/)** - Type-safe JavaScript

### Styling & UI

- **[Tailwind CSS 4](https://tailwindcss.com/)** - Utility-first CSS framework
- **[shadcn/ui](https://ui.shadcn.com/)** - High-quality, accessible UI components
- **[Reka UI](https://reka-ui.com/)** - Vue component library
- **[Lucide Vue](https://lucide.dev/)** - Beautiful SVG icons
- **[tw-animate-css](https://github.com/jjranalli/tw-animate-css)** - Animation utilities

### WebRTC & Media

- **WebRTC API** - Native browser peer-to-peer communication
- **MediaStream API** - Camera and microphone access

### Development Tools

- **[Vue TSC](https://github.com/vuejs/language-tools)** - TypeScript compiler for Vue
- **[VueUse](https://vueuse.org/)** - Essential Vue composition utilities

## 📋 Prerequisites

Before running this project, make sure you have the following installed:

- **Node.js** (v18 or higher)
- **pnpm** (recommended) or npm/yarn
- **Modern web browser** with WebRTC support (Chrome, Firefox, Safari, Edge)

## 🔧 Installation

1. **Clone the repository**

   ```bash
   git clone <repository-url>
   cd camouflage_client
   ```

2. **Install dependencies**

   ```bash
   pnpm install
   ```

3. **Start the development server**

   ```bash
   pnpm dev
   ```

4. **Open your browser**
   Navigate to `http://localhost:3000`

## 📝 Available Scripts

- `pnpm dev` - Start development server
- `pnpm build` - Build for production
- `pnpm generate` - Generate static site
- `pnpm preview` - Preview production build
- `pnpm lint:typescript` - Run TypeScript type checking
- `pnpm deepclean` - Clean all dependencies and lock files

## 🎯 How to Use

### Setting Up a Video Call

1. **Grant Camera Permissions**

   - When you first load the application, grant camera access when prompted
   - Your local video feed will appear in the "Local Video" section

2. **Create an Offer (Caller)**

   - Click the "Create Offer" button to generate an SDP offer
   - Copy the generated offer SDP from the text area
   - Share this offer with the person you want to connect with

3. **Create an Answer (Receiver)**

   - Paste the received offer SDP into the "Offer SDP" text area
   - Click "Create Answer" to generate a response
   - Copy the generated answer SDP and send it back to the caller

4. **Complete Connection (Caller)**
   - Paste the received answer SDP into the "Answer SDP" text area
   - Click "Add Answer" to establish the connection
   - Both parties should now see each other's video feeds

### Interface Components

- **Connection Status Badge**: Shows current connection state (Disconnected, Connecting, Connected, Failed)
- **Video Panels**: Local and remote video streams with loading states
- **Control Buttons**: Create Offer, Create Answer, Add Answer with tooltips
- **SDP Text Areas**: Copy/paste areas for offer and answer SDPs with copy/clear actions
- **Error Alerts**: Contextual error messages with auto-dismiss functionality

## 🏗️ Project Structure

```
camouflage_client/
├── app/
│   ├── app.vue                 # Root application component
│   ├── assets/
│   │   └── css/
│   │       └── main.css        # Global styles with Tailwind imports
│   ├── components/
│   │   └── ui/                 # shadcn/ui components
│   │       ├── alert/          # Alert components
│   │       ├── badge/          # Badge component
│   │       ├── button/         # Button component
│   │       ├── card/           # Card components
│   │       ├── separator/      # Separator component
│   │       ├── skeleton/       # Loading skeleton
│   │       ├── textarea/       # Textarea component
│   │       └── tooltip/        # Tooltip components
│   ├── lib/
│   │   └── utils.ts           # Utility functions (cn helper)
│   ├── pages/
│   │   └── index.vue          # Main WebRTC interface
│   └── plugins/
│       └── ssr-width.ts       # SSR width plugin
├── public/                     # Static assets
├── components.json             # shadcn/ui configuration
├── nuxt.config.ts             # Nuxt configuration
├── package.json               # Dependencies and scripts
└── tsconfig.json              # TypeScript configuration
```

## 🔐 Security Considerations

- **HTTPS Required**: WebRTC requires HTTPS in production environments
- **Camera Permissions**: The app requests camera access - users can deny or revoke permissions
- **No Audio**: Currently configured for video-only calls (audio disabled for privacy)
- **Direct P2P**: No server-side storage of video data - all communication is direct peer-to-peer

## 🚀 Deployment

### Build for Production

```bash
pnpm build
```

### Generate Static Site

```bash
pnpm generate
```

### Deploy to Platforms

This application can be deployed to any static hosting platform:

- **Vercel**: Automatic deployments with Git integration
- **Netlify**: Easy static site hosting
- **GitHub Pages**: Free hosting for open source projects
- **Cloudflare Pages**: Fast global CDN deployment

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

**Built with ❤️ using Nuxt 4, Vue 3, and WebRTC**
