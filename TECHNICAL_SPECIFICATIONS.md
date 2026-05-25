# Technical Specifications: Kefilwe wa Choppies

## 1. Data Input Schema
- **Flyer Feed**: CSV/JSON/PDF source containing `brand`, `size`, `before_price`, `after_price`.
- **Logic**: Automated calculation of `savings_percentage` using the `calculate_savings` tool.

## 2. Visual Rendering Engine
- **Format**: 9:16 Vertical (1080x1920).
- **Branding**: Hex codes for Choppies Red, Yellow, and White.
- **Dynamic Elements**: Rotating product PNGs with auto-scaling text containers for Pula values.
- **Ticker**: Scrolling text ticker at 60 words per minute for "Alternative Deals."

## 3. Linguistic & Voice Logic
- **Primary Model**: GPT-5 optimized for Botswana Vernacular.
- **Dialect Rules**: Inclusion of "Dumela Bagaetsho," "Cheap-cheap," and community-focused taglines.
- **Audio synthesis**: Modular phrases recorded by local artists, stitched programmatically.

## 4. Automation Pipeline
- **Trigger**: New flyer data upload.
- **Processing**:
    1. Data Extraction.
    2. Caption Generation.
    3. Video Composition.
- **Output**: Multi-format exports for WhatsApp Status, TikTok, and Reels.
