# AI-Powered Blog Post Editor

A modern, responsive Vue.js application that helps content creators draft blog posts with AI-powered suggestions. Built with Nuxt.js, Bootstrap, and OpenAI's GPT models, this application provides an intuitive interface for writing, editing, and managing blog post drafts with intelligent assistance.

![AI Blog Assistant](https://via.placeholder.com/800x400/2563eb/ffffff?text=AI+Blog+Assistant)

## ✨ Features

### 🤖 AI-Powered Assistance
- **Smart Title Generation**: Generate SEO-friendly, engaging titles based on your content
- **Keyword Suggestions**: Get relevant tags and keywords for better discoverability
- **Content Summarization**: Create concise summaries of your blog posts
- **Real-time Suggestions**: AI analyzes your content as you write

### 📝 Rich Text Editing
- **Quill Editor Integration**: Professional rich text editing with formatting options
- **Fallback Editor**: Graceful degradation with a custom HTML editor if Quill fails to load
- **Real-time Preview**: See your content formatted as you type
- **HTML Content Support**: Full HTML editing capabilities

### 💾 Draft Management
- **Auto-save Functionality**: Never lose your work with intelligent draft saving
- **Multiple Drafts**: Save and manage up to 50 drafts locally
- **Draft History**: Track when drafts were created and modified
- **Quick Access**: Sidebar navigation for easy draft switching
- **Storage Management**: Automatic cleanup when storage limits are reached

### 📱 Responsive Design
- **Mobile-First**: Optimized for mobile devices with touch-friendly interfaces
- **Tablet Support**: Perfect layout for tablet users
- **Desktop Experience**: Full-featured desktop interface
- **Cross-Platform**: Works seamlessly across all devices

### 🎨 Modern UI/UX
- **Bootstrap 5**: Clean, professional design with Bootstrap components
- **Dark Sidebar**: Elegant dark theme for the navigation sidebar
- **Toast Notifications**: Non-intrusive feedback for user actions
- **Confirmation Dialogs**: Safe operations with user confirmation
- **Loading States**: Clear feedback during AI processing

### 🔒 Privacy & Security
- **Local Storage**: All drafts stored locally in your browser
- **API Key Security**: Secure handling of OpenAI API credentials
- **No Data Collection**: Your content never leaves your device (except for AI processing)

## 🚀 Quick Start

### Prerequisites

- Node.js 16.x or higher
- npm or yarn package manager
- OpenAI API key

### Installation

1. **Clone the repository**
   \`\`\`bash
   git clone https://github.com/yourusername/ai-blog-editor.git
   cd ai-blog-editor
   \`\`\`

2. **Install dependencies**
   \`\`\`bash
   npm install
   # or
   yarn install
   \`\`\`

3. **Configure environment variables**
   
   Create a \`.env\` file in the root directory:
   \`\`\`env
   NUXT_OPENAI_API_KEY=your_openai_api_key_here
   \`\`\`
   
   Get your API key from [OpenAI Platform](https://platform.openai.com/api-keys)

4. **Start the development server**
   \`\`\`bash
   npm run dev
   # or
   yarn dev
   \`\`\`

5. **Open your browser**
   
   Navigate to \`http://localhost:3000\`

## 🛠️ Technology Stack

### Frontend Framework
- **Nuxt.js 3**: Vue.js framework with SSR capabilities
- **Vue.js 3**: Progressive JavaScript framework
- **Composition API**: Modern Vue.js development approach

### Styling & UI
- **Bootstrap 5**: Responsive CSS framework
- **Lucide Icons**: Beautiful, customizable icons
- **Custom CSS**: Additional styling for enhanced UX

### Rich Text Editing
- **Quill Editor**: Powerful WYSIWYG editor
- **@vueup/vue-quill**: Vue.js integration for Quill
- **Custom Fallback**: HTML-based editor for compatibility

### AI Integration
- **OpenAI GPT-4**: Advanced language model for content generation
- **Vercel AI SDK**: Streamlined AI integration
- **@ai-sdk/openai**: OpenAI provider for the AI SDK

### State Management
- **Vue Composition API**: Reactive state management
- **Local Storage**: Browser-based data persistence
- **Ref/Reactive**: Vue.js reactivity system

## 📁 Project Structure

\`\`\`
ai-blog-editor/
├── components/                 # Vue.js components
│   ├── AISuggestionPanel.vue  # AI suggestions interface
│   ├── BlogEditor.vue         # Main blog editing component
│   ├── ClientOnlyQuillEditor.vue # Quill editor wrapper
│   ├── ConfirmationDialog.vue # Modal confirmation dialogs
│   ├── FallbackEditor.vue     # Backup HTML editor
│   ├── LoadingSpinner.vue     # Loading state component
│   ├── SuggestionCard.vue     # Individual suggestion display
│   ├── ToastContainer.vue     # Toast notification system
│   └── ToastNotification.vue  # Individual toast messages
├── pages/                     # Nuxt.js pages
│   └── index.vue             # Main application page
├── plugins/                   # Nuxt.js plugins
│   └── bootstrap.client.js    # Bootstrap JavaScript integration
├── assets/                    # Static assets
│   └── css/
│       └── main.css          # Global styles
├── nuxt.config.ts            # Nuxt.js configuration
├── package.json              # Project dependencies
├── .env                      # Environment variables
└── README.md                 # Project documentation
\`\`\`

## 🎯 Usage Guide

### Creating Your First Blog Post

1. **Start Writing**: Click the "New" button or start typing in the editor
2. **Add Content**: Use the rich text editor to format your content
3. **Generate Title**: Click "Generate Title" for AI-powered title suggestions
4. **Add Keywords**: Use "Suggest Keywords" for SEO-friendly tags
5. **Create Summary**: Generate a concise summary of your content
6. **Save Draft**: Click "Save Draft" to store your work locally

### Managing Drafts

- **View Drafts**: All saved drafts appear in the left sidebar
- **Load Draft**: Click on any draft to load it into the editor
- **Delete Draft**: Use the trash icon to remove unwanted drafts
- **Auto-save**: Drafts are automatically saved when creating new ones

### AI Suggestions

#### Title Generation
- Analyzes your content to suggest engaging, SEO-friendly titles
- Click "Apply" to use the suggested title
- Generate multiple options by clicking the button again

#### Keyword Suggestions
- Provides relevant tags and keywords based on your content
- Automatically formatted as comma-separated values
- Helps improve SEO and content discoverability

#### Content Summarization
- Creates concise summaries of your blog posts
- Perfect for meta descriptions or social media
- Read-only display (no apply button)

### Mobile Experience

- **Responsive Design**: Optimized for all screen sizes
- **Touch-Friendly**: Large buttons and touch targets
- **Mobile Sidebar**: Collapsible navigation for mobile devices
- **Gesture Support**: Intuitive mobile interactions

## ⚙️ Configuration

### Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| \`NUXT_OPENAI_API_KEY\` | Your OpenAI API key | Yes |

### Nuxt.js Configuration

The application is configured in \`nuxt.config.ts\`:

\`\`\`typescript
export default defineNuxtConfig({
  runtimeConfig: {
    public: {
      openaiApiKey: process.env.NUXT_OPENAI_API_KEY
    }
  },
  css: ['~/assets/css/main.css'],
  app: {
    head: {
      script: [
        {
          src: 'https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js',
          tagPosition: 'bodyClose'
        }
      ]
    }
  }
})
\`\`\`

### Storage Configuration

- **Maximum Drafts**: 50 drafts (configurable in \`App.vue\`)
- **Storage Warning**: Appears when approaching 40 drafts
- **Auto-cleanup**: Keeps 20 most recent drafts when storage is full

## 🔧 Development

### Available Scripts

\`\`\`bash
# Development server
npm run dev

# Build for production
npm run build

# Generate static site
npm run generate

# Preview production build
npm run preview
\`\`\`

### Adding New Features

1. **Components**: Add new Vue components in the \`components/\` directory
2. **Pages**: Create new pages in the \`pages/\` directory
3. **Styling**: Add custom styles to \`assets/css/main.css\`
4. **AI Features**: Extend AI functionality in \`AISuggestionPanel.vue\`

### Code Style

- **Vue.js 3 Composition API**: Use \`<script setup>\` syntax
- **TypeScript**: Preferred for type safety
- **Bootstrap Classes**: Use Bootstrap utilities for styling
- **Responsive Design**: Mobile-first approach

## 🚀 Deployment

### Vercel (Recommended)

1. **Connect Repository**: Link your GitHub repository to Vercel
2. **Environment Variables**: Add \`NUXT_OPENAI_API_KEY\` in Vercel dashboard
3. **Deploy**: Automatic deployment on every push

### Netlify

1. **Build Command**: \`npm run generate\`
2. **Publish Directory**: \`dist\`
3. **Environment Variables**: Add API key in Netlify settings

### Self-Hosted

1. **Build**: \`npm run build\`
2. **Start**: \`npm run preview\`
3. **Environment**: Ensure API key is available

## 🐛 Troubleshooting

### Common Issues

#### "Could not resolve component" Error
- Ensure all component files exist in the \`components/\` directory
- Check file naming and case sensitivity
- Verify import paths use \`~/components/\` prefix

#### AI Suggestions Not Working
- Verify OpenAI API key is correctly set
- Check API key has sufficient credits
- Ensure internet connection is stable

#### Editor Not Loading
- Check browser console for JavaScript errors
- Verify Quill editor dependencies are installed
- Fallback editor should load if Quill fails

#### Mobile Issues
- Clear browser cache and reload
- Check responsive breakpoints in CSS
- Verify touch events are properly handled

### Performance Optimization

- **Lazy Loading**: Components load only when needed
- **Code Splitting**: Automatic with Nuxt.js
- **Image Optimization**: Use appropriate image formats
- **Caching**: Browser caching for static assets

## 🤝 Contributing

We welcome contributions! Please follow these steps:

1. **Fork the Repository**
2. **Create Feature Branch**: \`git checkout -b feature/amazing-feature\`
3. **Commit Changes**: \`git commit -m 'Add amazing feature'\`
4. **Push to Branch**: \`git push origin feature/amazing-feature\`
5. **Open Pull Request**

### Development Guidelines

- Follow Vue.js best practices
- Write descriptive commit messages
- Add comments for complex logic
- Test on multiple devices
- Update documentation as needed

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **OpenAI**: For providing powerful AI capabilities
- **Vue.js Team**: For the excellent framework
- **Nuxt.js Team**: For the amazing meta-framework
- **Bootstrap Team**: For the responsive CSS framework
- **Quill Team**: For the rich text editor
- **Vercel**: For the AI SDK and deployment platform

## 📞 Support

- **Documentation**: Check this README for detailed information
- **Issues**: Report bugs on GitHub Issues
- **Discussions**: Join community discussions
- **Email**: Contact support at your-email@example.com

## 🔮 Roadmap

### Upcoming Features

- [ ] **Export Options**: PDF, Markdown, HTML export
- [ ] **Collaboration**: Real-time collaborative editing
- [ ] **Templates**: Pre-built blog post templates
- [ ] **SEO Analysis**: Advanced SEO scoring and suggestions
- [ ] **Image Integration**: AI-powered image suggestions
- [ ] **Voice Input**: Speech-to-text functionality
- [ ] **Offline Mode**: Work without internet connection
- [ ] **Cloud Sync**: Sync drafts across devices
- [ ] **Analytics**: Content performance insights
- [ ] **Multi-language**: Support for multiple languages

### Version History

- **v1.0.0**: Initial release with core features
- **v1.1.0**: Added mobile responsiveness
- **v1.2.0**: Improved AI suggestions
- **v1.3.0**: Enhanced draft management
- **v1.4.0**: Bootstrap migration and UI improvements

---

**Made with ❤️ by Abdullahi Shobaloju**

*Happy blogging! 🚀*
