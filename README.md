# ContentHub AI - Twitter Content Automation SaaS

AI-Powered Twitter (X) Content Automation platform that generates and schedules tweets automatically using OpenAI GPT-5.1.

## Features

- 🤖 **AI-Powered Content Generation**: Automatically generate engaging tweets using GPT-5.1
- 🐦 **Twitter OAuth Integration**: Secure Twitter account connection
- ⏰ **Smart Scheduling**: Automated posting with customizable frequency and timing
- 📊 **Analytics Dashboard**: Track post performance and statistics
- ⚙️ **Content Configuration**: Customize topic, tone, length, hashtags, and emojis
- 🔒 **Secure Authentication**: JWT-based user authentication

## Tech Stack

### Backend
- **FastAPI** (Python) - Modern, fast web framework
- **MongoDB** - NoSQL database for data persistence
- **APScheduler** - Background job scheduling
- **OAuth 1.0a** - Twitter API authentication
- **OpenAI GPT-5.1** (via Emergent LLM Key) - AI tweet generation

### Frontend
- **React** - UI library
- **React Router** - Navigation
- **Tailwind CSS** - Styling
- **Shadcn UI** - Component library
- **Axios** - HTTP client
- **Sonner** - Toast notifications

## Setup Instructions

### Prerequisites

1. **Twitter Developer Account**
   - Go to [developer.twitter.com](https://developer.twitter.com/)
   - Create a new app
   - Enable OAuth 1.0a with Read and Write permissions
   - Set callback URL to: `https://x-content-hub-2.preview.emergentagent.com/twitter-callback`
   - Note down your API Key (Consumer Key) and API Secret (Consumer Secret)

2. **Environment Setup**
   - The platform already includes the Emergent LLM Key for OpenAI access
   - No separate OpenAI API key is required

### Backend Configuration

1. Add your Twitter API credentials to `/app/backend/.env`:
```bash
TWITTER_CLIENT_ID=your_twitter_consumer_key
TWITTER_CLIENT_SECRET=your_twitter_consumer_secret
```

2. Restart the backend:
```bash
sudo supervisorctl restart backend
```

### Using the Application

1. **Sign Up / Login**
   - Visit the landing page
   - Click "Get Started" and create an account

2. **Connect Twitter Account**
   - Go to Dashboard > Twitter tab
   - Click "Connect Twitter Account"
   - Authorize the application

3. **Configure Content**
   - Go to Content tab
   - Set your topic (e.g., "AI, Technology")
   - Choose tone, length, hashtags, and emoji preferences
   - Save configuration

4. **Set Schedule**
   - Go to Schedule tab
   - Set frequency (hourly, daily, weekly)
   - Set time of day
   - Enable automation

5. **Test Posting**
   - Go to Content tab
   - Click "Generate & Post Now" to test

## How It Works

### Tweet Generation Flow

1. User configures content preferences (topic, tone, length)
2. Scheduler triggers at configured intervals
3. System calls OpenAI GPT-5.1 with user preferences
4. AI generates a tweet within 280 characters
5. Tweet is posted to Twitter via OAuth 1.0a
6. Result is logged in the database

### Security

- Passwords are hashed using bcrypt
- JWT tokens for stateless authentication
- Twitter OAuth tokens are stored securely
- CORS configured for frontend-backend communication

## API Endpoints

### Authentication
- `POST /api/auth/signup` - Create new account
- `POST /api/auth/login` - Login
- `GET /api/auth/me` - Get current user

### Twitter
- `GET /api/twitter/auth-url` - Get Twitter OAuth URL
- `POST /api/twitter/callback` - Handle OAuth callback
- `GET /api/twitter/account` - Get connected account
- `DELETE /api/twitter/disconnect` - Disconnect account

### Content
- `POST /api/content-config` - Save content configuration
- `GET /api/content-config` - Get content configuration

### Scheduling
- `POST /api/schedule` - Save schedule
- `GET /api/schedule` - Get schedule
- `PATCH /api/schedule/toggle` - Enable/disable automation

### Posts
- `POST /api/posts/generate` - Generate and post tweet now
- `GET /api/posts` - Get post history
- `GET /api/stats` - Get statistics

## Database Schema

### Collections

1. **users**
   - id, email, password (hashed), name, created_at

2. **twitter_accounts**
   - user_id, twitter_id, screen_name, name, profile_image_url, oauth_token, oauth_token_secret, connected_at

3. **content_configs**
   - id, user_id, topic, tone, length, hashtags, emojis, created_at, updated_at

4. **schedules**
   - id, user_id, frequency, time_of_day, timezone, enabled, created_at, updated_at

5. **posts**
   - id, user_id, content, twitter_id, status, error_message, created_at, posted_at

## Scheduler System

- Uses APScheduler with AsyncIO
- Runs every hour on the hour
- Processes all enabled schedules
- Generates and posts tweets for each user
- Handles errors gracefully with retry logic

## Troubleshooting

### Twitter Connection Issues

1. **"Twitter API credentials not configured"**
   - Add TWITTER_CLIENT_ID and TWITTER_CLIENT_SECRET to `/app/backend/.env`
   - Restart backend with `sudo supervisorctl restart backend`

2. **OAuth Callback Error**
   - Ensure callback URL in Twitter Developer portal matches exactly:
     `https://x-content-hub-2.preview.emergentagent.com/twitter-callback`

3. **Tweet Posting Fails**
   - Check Twitter app has Read and Write permissions
   - Verify OAuth tokens are valid
   - Check rate limits

### AI Generation Issues

1. **"Failed to generate tweet"**
   - Emergent LLM Key is pre-configured
   - If balance is low, add credits in Profile > Universal Key
   - Check backend logs: `tail -f /var/log/supervisor/backend.err.log`

## Testing

Test the application flow:

1. Create account
2. Connect Twitter (requires Twitter Developer account)
3. Configure content preferences
4. Generate test post
5. Set schedule and enable automation
6. Check post history in Overview tab

## Production Considerations

- Use strong JWT_SECRET in production
- Implement rate limiting
- Add user quotas for posts
- Encrypt OAuth tokens at rest
- Add comprehensive error logging
- Implement monitoring and alerts
- Add backup strategy for database
- Consider Redis for scheduler state

## Scaling Strategy

1. **MVP → Phase 2**
   - Add analytics (impressions, engagement)
   - Multiple Twitter accounts per user
   - Thread generation
   - Image generation and posting
   - Content calendar view

2. **Monetization**
   - Free tier: 10 posts/month
   - Pro tier: 100 posts/month + analytics
   - Business tier: Unlimited + team features

3. **Enterprise Features**
   - Team collaboration
   - Approval workflows
   - Brand guidelines enforcement
   - Multi-platform support (LinkedIn, Facebook)

## License

MIT License

## Support

For issues or questions, please check:
- Backend logs: `tail -f /var/log/supervisor/backend.err.log`
- Frontend logs: Browser console
- API responses for detailed error messages