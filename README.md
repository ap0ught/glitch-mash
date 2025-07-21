# Glitch Mash

A social voting platform for Glitch game avatars and outfits, where players can showcase their creativity and vote for their favorite avatar combinations in head-to-head competitions.

## What is Glitch Mash?

Glitch Mash is a web application that connects to the Glitch game universe to create a democratic voting system for player avatars. It transforms avatar customization from a solitary activity into a social, competitive experience where fashion meets community engagement.

### 🎮 Core Concept

Players import their Glitch avatars and participate in continuous head-to-head voting battles. Think "Hot or Not" but for virtual fashion in the whimsical world of Glitch. Each vote contributes to a global ranking system that celebrates the most creative and popular outfit combinations.

### 🌟 Why This Matters

**For Players:**
- **Recognition**: Get your creative outfit combinations noticed by the community
- **Inspiration**: Discover new clothing combinations and styling ideas from other players
- **Competition**: Engage in friendly fashion competition with measurable results
- **Community**: Connect with other players through shared appreciation of avatar creativity

**For the Game Ecosystem:**
- **Engagement**: Extends Glitch gameplay beyond the main game client
- **Social Layer**: Creates additional social interaction opportunities
- **Content Discovery**: Helps players discover clothing items they might want to acquire
- **Community Building**: Reinforces the collaborative, creative spirit of Glitch

**For the Larger Gaming Context:**
- **Cross-Platform Integration**: Demonstrates how game APIs can create rich secondary experiences
- **Community-Driven Content**: Shows how player creativity can be celebrated and quantified
- **Social Gaming**: Extends the game's social aspects into web-based platforms
- **Data Insights**: Provides valuable feedback about popular clothing combinations and player preferences

## Features

### 🗳️ Avatar Voting System
- **Head-to-Head Battles**: Vote between two randomly selected avatars
- **Smart Matching**: Algorithm ensures diverse matchups while giving newer avatars exposure
- **Vote Tracking**: Comprehensive statistics for wins, losses, and voting ratios
- **Recent Battles**: View voting history for any avatar

### 👤 Player Profiles
- **Avatar Gallery**: Showcase all your imported avatar configurations
- **Switching System**: Activate different outfits as your "current" avatar
- **Performance Stats**: Track how well your avatars perform in votes
- **Battle History**: See recent victories and defeats

### 🎨 Clothing Integration
- **Detailed Breakdown**: View individual clothing items that make up each avatar
- **Clothing Database**: Comprehensive catalog of all Glitch clothing items
- **Metadata Tracking**: Store pricing, subscriber status, and rarity information
- **Visual Assets**: High-quality images for all clothing items and avatars

### 🔐 Secure Authentication
- **OAuth Integration**: Seamless login through your Glitch game account
- **Player Verification**: Ensures authentic player participation
- **Session Management**: Secure, persistent login sessions

## How It Works

### For Voters
1. **Log In**: Authenticate using your Glitch game account
2. **Vote**: Choose your favorite between two presented avatars
3. **Repeat**: Continue voting to influence the community rankings
4. **Explore**: Browse individual avatar profiles and player galleries

### For Avatar Owners
1. **Import**: Connect your Glitch account to import your avatar configurations
2. **Compete**: Your avatars automatically enter the voting pool
3. **Update**: Re-import to showcase new outfit combinations
4. **Track**: Monitor your avatars' performance and popularity

### The Algorithm
- **Fair Exposure**: New avatars get matched against established ones for fair evaluation
- **Volume Weighting**: Avatars need minimum vote counts before achieving "enough votes" status
- **Ratio Calculation**: Win percentage determines overall ranking
- **Duplicate Prevention**: Voters won't see the same matchup repeatedly

## Technical Stack

- **Framework**: Built on Flamework (PHP-based framework inspired by Flickr's architecture)
- **Database**: MySQL for storing votes, avatars, players, and clothing data
- **Templating**: Smarty template engine for clean HTML output
- **API Integration**: RESTful communication with Glitch game servers
- **Authentication**: OAuth 2.0 implementation for secure player verification

## Relationship to @ap0ught/glitch-avatars

This project likely integrates with or extends the avatar generation capabilities provided by the `@ap0ught/glitch-avatars` repository. While Glitch Mash focuses on the social voting and competition aspects, the avatar generation system provides the foundational technology for creating and rendering the avatar images that players vote on.

The synergy between these projects demonstrates how the Glitch ecosystem can be extended:
- **Avatar Generation**: Technical infrastructure for creating avatar visualizations
- **Social Platform**: Community engagement and voting system (this project)
- **Game Integration**: Seamless connection to the main Glitch game experience

## Setup and Installation

### Prerequisites
- PHP 7.4+ with MySQL support
- MySQL 5.7+ database server
- Web server (Apache/Nginx) with mod_rewrite
- Glitch API credentials (client ID and secret)

### Configuration
1. Copy `include/config.php.example` to `include/config.php`
2. Configure database connection settings
3. Set up Glitch API credentials
4. Configure authentication cookie settings
5. Set appropriate file permissions for template compilation

### Database Setup
Create the required database tables:
- `glitchmash_players`: Player account information
- `glitchmash_avatars`: Avatar data and voting statistics  
- `glitchmash_votes`: Individual vote records
- `glitchmash_clothing`: Clothing item catalog

### Deployment
1. Upload files to your web server
2. Ensure `templates_c/` directory is writable
3. Configure your web server to handle URL rewriting
4. Test OAuth authentication flow
5. Import initial avatar data

## Contributing

This project welcomes contributions that enhance the voting experience, improve the user interface, or extend integration with the Glitch game ecosystem. Areas of particular interest include:

- **UI/UX Improvements**: Making the voting experience more engaging
- **Performance Optimization**: Handling larger volumes of avatars and votes
- **API Enhancements**: Deeper integration with Glitch game features
- **Community Features**: Adding social elements like comments or favorites
- **Analytics**: Better insights into voting patterns and trends

## License

This project demonstrates the potential for community-driven applications built on game APIs, encouraging similar innovations in the broader gaming ecosystem.

---

*Experience the creative spirit of Glitch through community-driven avatar fashion competitions. Where virtual style meets social validation.*