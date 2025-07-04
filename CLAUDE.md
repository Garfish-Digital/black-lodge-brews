# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview
Black Lodge Brews is a premium craft brewery website built with Astro. This is the fourth demo in a portfolio showcase series, focusing on sophisticated liquid effects and animations. The design system emphasizes mystery and premium craft brewing aesthetics.

## Development Commands
- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build

## Architecture & Structure

### Design System
**Colors:**
- Primary: Rich brown (#3E2723), Roasted malt (#1A0E0A)
- Secondary: Foam cream (#F5E6D3), Dark forest (#1B3A1B)
- Accent: Amber gold (#FFA000), Beer foam (#FFFBF0)

**Typography:**
- Display: Playfair Display (serif) for headings
- Body: Inter (sans-serif) for content

### Key Components

**LiquidTransition.astro** - Handles smooth page transitions with liquid flow effects using SVG paths and CSS animations.

**BeerCard.astro** - Interactive beer showcase cards with:
- Animated beer glass that fills on hover
- Bubble animations and ripple effects
- Color-coded beer types with realistic liquid colors
- Intersection Observer for scroll-triggered animations

**MistParticles.astro** - Canvas-based particle system for atmospheric effects:
- Floating mist particles with realistic physics
- Configurable particle count, size, speed, and opacity
- Performance optimized with visibility change handling

**PourAnimation.astro** - Complex beer pouring simulation:
- Multiple glass types (pint, tulip, snifter)
- Animated pour stream with liquid physics
- Foam effects and splash animations
- Auto-start and manual trigger options

### Liquid Effects System
The `_liquid-effects.scss` file contains specialized animations:
- `liquidFlow` - Morphing clip-path animations
- `beerPour` - Realistic beer filling effects
- `foamBubble` - Bubble rise animations
- `ripple` - Interactive ripple effects on hover
- `wave` - Subtle wave motions for ambient movement

### Layout & Styling
- Mobile-first responsive design
- CSS Grid and Flexbox for layouts
- SCSS with organized variable system
- Smooth scroll behavior and intersection observers
- Custom scrollbar styling
- Gradient backgrounds with craft brewery aesthetics

## Content Structure
The site includes:
- Hero section with animated beer pour
- Tap list with 6 craft beers (including availability states)
- Story section with mist particle effects
- Taproom section with event calendar
- Contact section with location details

## Performance Considerations
- Mist particles use requestAnimationFrame for smooth animations
- Intersection Observer API for scroll-triggered effects
- Visibility API to pause animations when tab is hidden
- Optimized CSS animations using GPU acceleration
- Minimal JavaScript footprint with vanilla implementations

## Browser Compatibility
- Modern browsers with CSS Grid support
- Canvas API for particle effects
- IntersectionObserver API (with fallback graceful degradation)
- CSS custom properties for dynamic theming