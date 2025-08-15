# Article Master

An intelligent article generation tool designed to create engaging content for multiple social media platforms including Twitter, 小红书 (Xiaohongshu), and 微信公众号 (WeChat Official Account).

## Overview

Article Master helps you quickly transform news, updates, and discoveries into platform-optimized articles. Whether you've discovered a new feature in Claude Code or found an interesting tech update, this tool generates tailored content for your social media accounts.

## Features

- **Multi-platform content generation**: Create articles optimized for different social media platforms
- **Intelligent adaptation**: Automatically adjusts tone, length, and format for each platform
- **Quick content creation**: Transform ideas into publish-ready articles
- **Social media optimization**: Platform-specific formatting and hashtag suggestions

## Supported Platforms

- **Twitter**: Short-form, engaging tweets with relevant hashtags
- **小红书 (Xiaohongshu)**: Visual-friendly content with lifestyle appeal
- **微信公众号 (WeChat Official Account)**: Long-form, professional articles

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd article_master
```

2. Create and activate virtual environment:
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

## Usage

### Basic Usage

```python
python main.py
```

### Example Workflow

1. **Input**: "Claude Code released a new feature for background task management"
2. **Process**: The tool analyzes the content and generates platform-specific articles
3. **Output**: Three optimized articles ready for posting

### Platform-Specific Features

#### Twitter
- Character limit optimization
- Trending hashtag suggestions
- Thread creation for longer content

#### 小红书 (Xiaohongshu)
- Emoji integration
- Visual content suggestions
- Lifestyle angle adaptation

#### 微信公众号 (WeChat Official Account)
- Professional tone
- Detailed explanations
- Structured formatting

## Configuration

Create a `config.py` file to customize:
- Platform preferences
- Content style settings
- API keys (if using external services)

## Example Use Cases

- **Tech Updates**: "Claude Code now supports background processes" → Platform-optimized announcements
- **Feature Discoveries**: New tool features → Educational content for each platform
- **Industry News**: Latest developments → Engaging social media posts
- **Tutorial Content**: How-to guides → Platform-appropriate instructional posts

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

[Add your preferred license]

## Contact

[Add your contact information]