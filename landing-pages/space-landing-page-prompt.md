```md
Build a full-screen, highly polished cinematic landing page for a space travel website using React, Vite, Tailwind CSS, lucide-react, and the framer-motion library.
Output EVERYTHING into a single App.jsx file.
Do not omit any code; provide the complete, working file.
1. Global Design System & TypographyBackground & Theme: The page background must be #02040A (deep space void).
  Set text selection to selection:bg-white/30 selection:text-white.Fonts: Inject this into the document dynamically:@import url('https://fonts.googleapis.com/css2?family=Instrument+Serif:ital@0;1&family=Barlow:wght@300;400;500;600&display=swap');
  Tailwind Config Mapping: Assume font-heading maps to 'Instrument Serif' and font-body maps to 'Barlow'.
  Use font-heading italic for all major headlines. Use font-body for all paragraphs, buttons, and UI text.
  Typography Polish: Use text-balance, tracking-tight on large headings, and tracking-[0.2em] to tracking-[0.25em] on small uppercase labels.
  Increase body text legibility using text-white/60 or text-white/70 with leading-relaxed or leading-loose.
2. The "Liquid Glass" CSSInject the following exact CSS rules into the document using a <style dangerouslySetInnerHTML={{ __html: globalStyles }} /> block inside the main wrapper:
@layer components {

  .liquid-glass {
    background: rgba(255, 255, 255, 0.015);
    background-blend-mode: luminosity;
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    border: none;
    box-shadow: inset 0 1px 1px rgba(255, 255, 255, 0.1), 0 4px 24px rgba(0, 0, 0, 0.2);
    position: relative;
    overflow: hidden;
    transition: all 0.4s cubic-bezier(0.16, 1, 0.3, 1);
  }

  .liquid-glass::before {
    content: ''; position: absolute; inset: 0; border-radius: inherit; padding: 1px;
    background: linear-gradient(180deg, rgba(255,255,255,0.3) 0%, rgba(255,255,255,0.05) 20%, rgba(255,255,255,0) 40%, rgba(255,255,255,0) 60%, rgba(255,255,255,0.05) 80%, rgba(255,255,255,0.2) 100%);
    -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
    -webkit-mask-composite: xor; mask-composite: exclude; pointer-events: none;
  }

  .liquid-glass-strong {
    background: rgba(255, 255, 255, 0.02);
    background-blend-mode: luminosity;
    backdrop-filter: blur(50px);
    -webkit-backdrop-filter: blur(50px);
    border: none;
    box-shadow: 0 8px 32px rgba(0,0,0,0.3), inset 0 1px 1px rgba(255,255,255,0.2);
    position: relative; overflow: hidden;
    transition: all 0.4s cubic-bezier(0.16, 1, 0.3, 1);
  }

  .liquid-glass-strong::before {
    content: ''; position: absolute; inset: 0; border-radius: inherit; padding: 1.2px;
    background: linear-gradient(180deg, rgba(255,255,255,0.6) 0%, rgba(255,255,255,0.15) 20%, rgba(255,255,255,0) 40%, rgba(255,255,255,0) 60%, rgba(255,255,255,0.15) 80%, rgba(255,255,255,0.4) 100%);
    -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
    -webkit-mask-composite: xor; mask-composite: exclude; pointer-events: none;
  }
}

3. Animation Paradigms
Global Easing: Use a custom cubic-bezier for ALL framer-motion transitions: ease: [0.16, 1, 0.3, 1].
BlurText Component: Create a custom component that splits a string into words. Use whileInView with staggerChildren. Each word animates from { filter: 'blur(12px)', opacity: 0, y: 40 } to { filter: 'blur(0px)', opacity: 1, y: 0 }.Scroll Reveals: Wrap all section content in motion.div using whileInView to fade up and remove blur as the user scrolls down. Use viewport={{ once: true, margin: "-50px" }}.4. Layout & SectionsBuild the following sections in order, all contained within <main>:A. Navbar (Fixed Top):z-50, fixed top-6, px-6 lg:px-12. Left: Logo (lucide Orbit) in a circular liquid-glass button. Right: Pill-shaped liquid-glass nav containing links (Home, Voyages, Worlds, Innovation, Launch) and a solid white "Claim a Spot" button with an ArrowUpRight icon.B. Hero Section (100vh):Parallax Video Background: Positioned absolute. Video URL: https://d8j0ntlcm91z4.cloudfront.net/user_38xzZboKViGWJOttwIXH07lWA1P/hf_20260306_115329_5e00c9c5-4d69-49b7-94c3-9c31c60bb644.mp4. Use useScroll and useTransform to apply a parallax y transform, opacity fade, and scale effect on scroll. Overlay a gradient map fading to #02040A at the bottom. Inject <link rel="preload"> tags for the video via useEffect.Content: Center aligned. "New" badge (liquid-glass pill). Massive BlurText heading: "Venture Past Our Sky Across the Universe". Subtitle paragraph. Two CTA buttons (Primary: liquid-glass-strong, Secondary: Watch video with Play icon).C. Mission Statement:Centered layout. A vertical gradient line fading in. BlurText: "We are not just building ships. We are engineering the next great chapter of human history." Ambient blue glowing orb blur-[120px] behind it.D. Vessel Specs (Split Layout):Left: Massive 650px tall rounded-[2.5rem] liquid-glass card containing an image (https://images.unsplash.com/photo-1451187580459-43490279c0fa?q=80&w=1600&auto=format&fit=crop). Text overlay at bottom: "Odyssey Class" and "The Apex Voyager". Image scales slightly on hover.Right: Heading "Unrivaled Engineering". Grid of 6 smaller liquid-glass cards (Speed, Integrity, Capacity, Life Support, Shielding, Comms) with icons and values.E. Features Grid:Heading: "Engineering the Impossible". Ambient indigo glowing orb in background.3-column grid of 6 liquid-glass-strong cards. Each has an icon inside an inner rounded-2xl border, a title, and description. Add subtle hover -translate-y-1 effects.F. Journey Timeline:Vertical alternating timeline. A central sleek vertical gradient line.4 Steps: Atmospheric Ascent, Orbital Transfer, Deep Space Cruise, Destination Arrival.Each step has a glowing timeline node on the center line. The content card is liquid-glass, sitting on alternating left/right sides (on desktop).G. Destinations (Bento Grid):Heading: "Worlds Await" with "View All Destinations" link.Grid of massive image cards with gradient overlays. Array of 4 (Mars, Lunar Gateway, Titan, Europa). Items 1 and 4 should span 2 columns on desktop (md:col-span-2). Text/buttons bottom-aligned inside the cards. Group hover effects on images (slow scale).H. FAQ (Accordion):Heading: "Common Inquiries".4 Questions. Map through them creating liquid-glass buttons. Use lucide-react ChevronDown that rotates when open. Use Framer Motion's <AnimatePresence> to animate the height 0 to auto for the answers.I. Footer:Top border. Multi-column layout.Left col: Logo, big heading "The universe is calling", Email input (liquid-glass) + Subscribe button.Middle/Right cols: Links for Missions and Company.Bottom bar: Copyright, policy links, and a "Partners" list (Aeon, Vela, Zeno) in italic heading font.
```
