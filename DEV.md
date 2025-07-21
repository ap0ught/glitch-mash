# Glitch Mash - Developer Documentation

## Architecture Overview

Glitch Mash is built on the Flamework PHP framework and implements a sophisticated voting system that integrates deeply with the Glitch game API. Understanding each component's role is crucial for extending or maintaining the application.

## Core Components

### 🔐 Authentication System (`auth.php`)

**Purpose**: Implements OAuth 2.0 flow with Glitch game servers for secure player authentication.

**Significance**: 
- Ensures only legitimate Glitch players can participate in voting
- Maintains connection to player's game identity and avatar data
- Provides foundation for all personalized features

**Implementation Details**:
```php
// OAuth flow stages:
1. Redirect to Glitch OAuth endpoint
2. Handle callback with authorization code
3. Exchange code for access token
4. Store token and fetch player identity
5. Create/update player record in local database
```

**Larger Game Context**:
- **Player Verification**: Prevents spam votes from non-players
- **Cross-Platform Identity**: Links web voting to in-game identity
- **API Rate Limiting**: Proper authentication enables higher API quotas
- **Future Integration**: Token can be used for expanded game features

### 📥 Avatar Import System (`import.php`)

**Purpose**: Fetches and stores player avatar configurations from the Glitch API.

**Significance**:
- Bridges game data with voting platform
- Captures detailed clothing metadata for analysis
- Enables real-time outfit updates

**Technical Flow**:
```php
1. Call `/simple/players.getClothing` API endpoint
2. Parse clothing data for each equipment slot
3. Store individual clothing items in database
4. Serialize complete outfit configuration
5. Mark latest import as "active" avatar
6. Deactivate previous avatar versions
```

**Database Impact**:
- Updates `glitchmash_clothing` with item metadata
- Creates new `glitchmash_avatars` record
- Maintains version history of player outfits

**Larger Game Context**:
- **Fashion Evolution**: Tracks how player style changes over time
- **Item Popularity**: Data mines which clothing combinations work
- **Economic Insights**: Subscriber-only items vs. free items performance
- **Content Discovery**: Helps players discover new clothing options

### 🗳️ Voting Engine (`index.php`)

**Purpose**: Core voting mechanism that presents avatar matchups and processes vote results.

**Significance**:
- Heart of the community engagement system
- Implements fair and engaging matchup algorithm
- Provides real-time feedback on avatar popularity

**Algorithm Breakdown**:
```php
// Smart matchup selection:
1. Query 3 pools: random, low-vote-count, medium-vote-count
2. Filter out previous matchup and voter's own avatars  
3. Shuffle and select 2 candidates
4. Generate cryptographically signed vote tokens
5. Present matchup with voting interface
```

**Vote Processing**:
```php
1. Validate vote signature to prevent tampering
2. Store vote with hash to prevent duplicate voting
3. Recalculate win/loss statistics for both avatars
4. Update "enough_votes" threshold status
5. Calculate and store win ratios
```

**Why This Algorithm Matters**:
- **New Player Inclusion**: Ensures fresh avatars get exposure
- **Statistical Validity**: Requires minimum votes before reliable ranking
- **Gaming Prevention**: Cryptographic signatures prevent vote manipulation
- **Performance Optimization**: Avoids expensive random queries on large datasets

**Larger Game Context**:
- **Community Building**: Creates shared experiences around avatar creativity
- **Trend Analysis**: Identifies popular clothing combinations and styles
- **Player Retention**: Gives players reason to return and check vote results
- **Social Proof**: Validates player's creative choices through peer voting

### 👤 Profile System (`you.php`, `avatar.php`)

**Purpose**: Provides player and avatar profile views with historical data and management tools.

**Significance**:
- Gives players ownership and control over their avatar presentation
- Provides detailed analytics on avatar performance
- Enables outfit switching and management

**Player Profile Features** (`you.php`):
```php
1. Display all player avatars (active and historical)
2. Show voting performance statistics
3. Enable avatar activation switching
4. Generate secure activation codes to prevent abuse
```

**Avatar Detail Pages** (`avatar.php`):
```php
1. Detailed clothing breakdown with item metadata
2. Recent battle history (wins and losses)
3. Statistical performance over time
4. Links to opponent avatars for comparison
```

**Data Relationships**:
- Player → Multiple Avatars (versioned)
- Avatar → Multiple Clothing Items (detailed)
- Avatar → Vote History (performance tracking)

**Larger Game Context**:
- **Player Investment**: Creates emotional attachment to avatar performance
- **Style Documentation**: Preserves fashion history for individual players
- **Competition**: Enables players to strategize about outfit effectiveness
- **Community Recognition**: Successful avatars become known and celebrated

## Database Schema Deep Dive

### `glitchmash_players`
```sql
- tsid: Unique Glitch player identifier
- oauth_token: Current API access token
- name: Player display name
- date_added: Account creation timestamp
```
**Significance**: Central identity hub linking game accounts to voting participation.

### `glitchmash_avatars`  
```sql
- id: Auto-increment primary key
- player_tsid: Foreign key to players table
- url: Direct link to avatar image (172px version)
- details: Serialized array of clothing item IDs per slot
- votes: Total number of votes received
- wins: Number of victories in voting
- ratio: Win percentage (wins/votes)
- enough_votes: Boolean flag for statistical reliability
- is_active: Current active avatar for player
- date_added/updated: Timestamps
```
**Significance**: Core voting entity with comprehensive performance tracking.

### `glitchmash_votes`
```sql
- player_tsid: Voter identity
- hash: Unique identifier for avatar pair
- win_id/lose_id: Avatar IDs for matchup result
- date_updated: Vote timestamp
```
**Significance**: Enables vote deduplication and historical analysis.

### `glitchmash_clothing`
```sql
- id: Glitch item ID
- name: Item display name
- url/image/image_small/image_large: Asset URLs
- sub_only: Subscriber exclusive flag
- credits: Item cost in game currency
- slot: Equipment slot (hat, shirt, pants, etc.)
```
**Significance**: Rich metadata for fashion analysis and item discovery.

## API Integration Points

### Glitch Game API Endpoints Used

**`/oauth2/authorize` & `/oauth2/token`**
- **Purpose**: Player authentication flow
- **Significance**: Establishes trusted connection to game identity
- **Rate Limits**: Standard OAuth limits apply

**`/simple/auth.check`**
- **Purpose**: Validates tokens and fetches player info
- **Usage**: Post-authentication identity verification
- **Returns**: Player TSID and display name

**`/simple/players.getClothing`**
- **Purpose**: Retrieves complete avatar configuration
- **Parameters**: `player_tsid` for target player
- **Returns**: Full clothing data with metadata and image URLs
- **Significance**: Primary data source for avatar importing

### Rate Limiting and Best Practices

**Import Frequency**: Players can re-import to update avatars, but system should implement reasonable rate limiting to prevent API abuse.

**Token Management**: OAuth tokens should be refreshed as needed and invalid tokens should trigger re-authentication.

**Error Handling**: Graceful degradation when API is unavailable, with cached data serving as fallback.

## Relationship to @ap0ught/glitch-avatars

### Integration Scenarios

**Avatar Rendering Pipeline**:
The `@ap0ught/glitch-avatars` project likely provides:
- **Image Generation**: Converting clothing item IDs to visual avatar representations
- **Asset Management**: Handling the complex layering of clothing items
- **Performance Optimization**: Caching and optimizing avatar image generation

**Synergy with Glitch Mash**:
```
glitch-avatars → generates visual representations
       ↓
glitch-mash → imports and presents for voting
       ↓
community → votes and creates rankings
       ↓
feedback → informs popular combinations
```

**Technical Integration Points**:
- Avatar image URLs stored in `glitchmash_avatars.url`
- Clothing item metadata in `glitchmash_clothing` table
- Potential for dynamic avatar generation based on vote winners

### Larger Ecosystem Benefits

**For Game Developers**:
- **Player Engagement Analytics**: Understand what avatar styles resonate
- **Content Validation**: Test clothing combinations before game release
- **Community Feedback Loop**: Direct input on cosmetic item popularity

**For Players**:
- **Style Inspiration**: Discover successful outfit combinations
- **Social Recognition**: Achieve recognition for creative fashion choices
- **Extended Gameplay**: Continue engaging with game identity outside main client

**For the Platform**:
- **Rich Content**: User-generated content through avatar combinations
- **Community Building**: Shared activities that strengthen player connections
- **Data Insights**: Valuable analytics on player preferences and behavior

## Performance Considerations

### Voting Algorithm Optimization

**Challenge**: Selecting random avatars becomes expensive with large datasets.

**Solution**: Three-tier selection strategy:
```php
1. Pure random (5 candidates) - ensures true randomness
2. Low-vote filter (5 candidates) - gives new avatars exposure  
3. Medium-vote filter (5 candidates) - balances exposure with quality
```

**Significance**: Maintains algorithm fairness while scaling to thousands of avatars.

### Database Query Optimization

**Vote Counting**:
```sql
-- Optimized with proper indexing
SELECT COUNT(*) FROM glitchmash_votes WHERE win_id = ?
SELECT COUNT(*) FROM glitchmash_votes WHERE lose_id = ?
```

**Avatar Selection**:
```sql
-- Uses compound indexes for performance
SELECT * FROM glitchmash_avatars 
WHERE is_active=1 AND votes < ? 
ORDER BY RAND() LIMIT 5
```

### Caching Strategies

**Avatar Images**: Leverage CDN caching for frequently accessed avatar images.

**Vote Statistics**: Consider caching win ratios and vote counts with periodic refresh.

**Player Data**: Cache active avatar information to reduce database hits.

## Security Considerations

### Vote Integrity

**Cryptographic Signatures**: Each vote includes SHA1 hash preventing vote manipulation:
```php
$sig = substr(sha1($secret . '/' . $win_id . '/' . $lose_id . '/' . $voter_tsid), 0, 20);
```

**Deduplication**: Hash-based system prevents multiple votes on same matchup by same player.

**Token Validation**: All votes validated against cryptographic signature before processing.

### Authentication Security

**OAuth Token Storage**: Tokens stored securely and refreshed as needed.

**Session Management**: Proper cookie handling with domain and security flags.

**CSRF Protection**: Crumb-based CSRF protection for state-changing operations.

## Extending the Platform

### Adding New Features

**Comments System**: 
- Add `glitchmash_comments` table
- Link to specific avatars or matchups
- Moderate content appropriately

**Favorite System**:
- Track which avatars players bookmark
- Use for personalized recommendations
- Influence matchup algorithm

**Tournament Mode**:
- Bracket-style elimination voting
- Seasonal competitions
- Special recognition for winners

**Enhanced Analytics**:
- Clothing item popularity rankings
- Trend analysis over time
- Player voting pattern insights

### API Extensions

**Public APIs**: Expose anonymized voting data for external analysis.

**Webhook Integration**: Real-time notifications for avatar performance.

**Mobile Apps**: JSON APIs for native mobile voting applications.

## Development Workflow

### Local Setup

1. **Environment Configuration**:
   ```bash
   cp include/config.php.example include/config.php
   # Edit database and API credentials
   ```

2. **Database Initialization**:
   ```sql
   CREATE DATABASE glitchmash;
   # Run schema creation scripts
   ```

3. **Permissions**:
   ```bash
   chmod 777 templates_c/  # Smarty compilation directory
   ```

### Testing Strategies

**Unit Tests**: Test vote processing logic with known datasets.

**Integration Tests**: Verify API integration with Glitch endpoints.

**Load Testing**: Simulate high voting volume to identify bottlenecks.

**Security Testing**: Verify vote signature validation and authentication flows.

### Deployment Considerations

**Environment Variables**: Store API credentials securely outside web root.

**Database Migrations**: Plan schema updates for new features.

**Monitoring**: Track API response times and error rates.

**Backup Strategy**: Regular backups of vote data and player information.

## Future Possibilities

### Advanced Analytics

**Machine Learning**: Predict successful outfit combinations based on historical votes.

**Style Classification**: Automatically categorize avatars by style themes.

**Trend Forecasting**: Identify emerging fashion trends in the community.

### Enhanced Gamification

**Voting Streaks**: Reward consistent voting participation.

**Style Challenges**: Theme-based voting events and competitions.

**Achievement System**: Recognition for various voting and avatar milestones.

### Cross-Game Integration

**Multi-Game Support**: Extend platform to other games with avatar systems.

**Item Trading**: Integration with in-game trading systems.

**Creator Economy**: Recognition and rewards for popular avatar creators.

---

*This platform demonstrates the power of community-driven content and the potential for extending game experiences through thoughtful API integration and social features.*