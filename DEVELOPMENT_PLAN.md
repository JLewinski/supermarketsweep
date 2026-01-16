# Supermarket Sweep - Development Plan

## Project Overview
Building a real-time Supermarket Sweep question game with separate host and contestant interfaces, WebSocket synchronization, and PostgreSQL persistence.

---

## Phase 1: Foundation & Setup (Week 1)

### 1.1 Database Design & Setup
- [ ] Design PostgreSQL schema for Questions, Rounds, Contestants, Results
- [ ] Create database migrations
- [ ] Set up database connection pooling
- [ ] Create seed data for testing (sample questions of each type)
- [ ] Write database utility functions (CRUD operations)

### 1.2 WebSocket Infrastructure
- [ ] Choose WebSocket library (e.g., Socket.io, ws, or native WebSockets)
- [ ] Set up WebSocket server endpoint
- [ ] Implement connection/reconnection logic
- [ ] Create message protocol for host ↔ contestant sync
- [ ] Test basic bidirectional communication

### 1.3 Project Configuration
- [ ] Set up environment variables (.env for database, WebSocket config)
- [ ] Configure SvelteKit server endpoints for WebSocket handling
- [ ] Set up development vs production configurations
- [ ] Add necessary dependencies (postgres client, WebSocket library, etc.)

---

## Phase 2: Core Game Logic (Week 2)

### 2.1 Question Type Implementations

#### Hangman-Style
- [ ] Create data model for letter reveal timing
- [ ] Implement progressive letter reveal logic with configurable delay
- [ ] Build UI component for blank spaces and revealed letters
- [ ] Add host control for triggering letter reveals

#### Progressive Clues
- [ ] Create data model for multi-clue questions
- [ ] Implement clue reveal timing logic (manual host control + optional auto-reveal)
- [ ] Build UI component for progressive clue display
- [ ] Add host controls for manual clue advancement

#### Fill in the Blank
- [ ] Create data model supporting multiple acceptable answers
- [ ] Implement answer validation logic
- [ ] Build UI component for sentence with blank
- [ ] Add host answer checking interface

#### Guess the Price
- [ ] Create data model for product + price data
- [ ] Implement price comparison logic
- [ ] Build UI component for 3-product display
- [ ] Add host control for marking correct guesses

### 2.2 Game State Management
- [ ] Create centralized game state store (round, current question, timer, scores)
- [ ] Implement question progression logic
- [ ] Build timer functionality (2 min default, configurable)
- [ ] Create scoring/bonus time calculation logic
- [ ] Handle game start, pause, reset functionality

---

## Phase 3: Host Interface (Week 3)

### 3.1 Question Management (CRUD)
- [ ] Create question list/library view
- [ ] Build question creation form (supporting all question types)
- [ ] Implement question editing interface
- [ ] Add question deletion with confirmation
- [ ] Create question type selector/switcher

### 3.2 Round Setup
- [ ] Build round creation interface
- [ ] Implement question selection for rounds (3 questions)
- [ ] Add contestant registration/management
- [ ] Create pre-game setup checklist

### 3.3 Game Control Panel
- [ ] Build live game control interface
  - Next question button
  - Reveal clue button (Progressive type)
  - Trigger letter reveal (Hangman type)
  - Mark contestant correct/incorrect
  - Pause/resume timer
- [ ] Show answer(s) to host (always visible)
- [ ] Display contestant status and responses
- [ ] Add contestant score/bonus time tracking
- [ ] Implement sound trigger controls (correct/incorrect sounds)

### 3.4 Host Dashboard
- [ ] Create overview of current game state
- [ ] Build real-time contestant tracking display
- [ ] Add timer visualization
- [ ] Implement clue reveal timeline (Progressive type)

---

## Phase 4: Contestant Interface (Week 4)

### 4.1 Question Display Components
- [ ] Create hangman-style letter reveal component
- [ ] Build progressive clue display component
- [ ] Create fill-in-the-blank display component
- [ ] Build price guess product display component
- [ ] Ensure large, readable fonts and clear layout

### 4.2 Game Status Display
- [ ] Build prominent timer component
- [ ] Create score/bonus time tracker
- [ ] Add current question indicator (Question 1 of 3)
- [ ] Implement real-time update handling

### 4.3 Contestant-Specific Features
- [ ] Display contestant name/identifier
- [ ] Show correct/incorrect feedback (sound + visual)
- [ ] Handle connection status indicator
- [ ] Implement waiting/standby screen

---

## Phase 5: Real-time Synchronization (Week 5)

### 5.1 WebSocket Event Handlers
- [ ] Implement host → contestant events:
  - Question change
  - Clue reveal
  - Letter reveal (Hangman)
  - Timer updates
  - Score updates
  - Correct/incorrect marking
- [ ] Implement contestant → host events:
  - Connection status
  - Acknowledgment receipts

### 5.2 State Synchronization
- [ ] Ensure consistent state between host and contestants
- [ ] Handle late-joining contestants (send current state)
- [ ] Implement reconnection state recovery
- [ ] Add conflict resolution for timing edge cases

### 5.3 Testing & Reliability
- [ ] Test with multiple simultaneous contestants
- [ ] Simulate network interruptions and reconnections
- [ ] Load test with expected contestant count
- [ ] Add error handling and fallback UI

---

## Phase 6: Database Integration (Week 6)

### 6.1 API Endpoints
- [ ] Create REST/GraphQL endpoints for:
  - Questions CRUD
  - Rounds CRUD
  - Contestants CRUD
  - Results retrieval
- [ ] Implement server-side validation
- [ ] Add error handling and logging

### 6.2 Data Persistence
- [ ] Save questions to database
- [ ] Store round configurations
- [ ] Record contestant data
- [ ] Save game results (optional for MVP)

### 6.3 Data Loading
- [ ] Load pre-created questions for rounds
- [ ] Fetch contestant information
- [ ] Retrieve historical results (if implemented)
- [ ] Implement caching strategy for performance

---

## Phase 7: Routing & Navigation (Week 7)

### 7.1 Page Routes
- [ ] `/questions/host` - Host control interface
- [ ] `/questions/contestant` - Contestant display
- [ ] `/questions/setup` - Pre-game question/round setup
- [ ] `/` - Landing page with role selection
- [ ] Handle route guards and access control (if needed)

### 7.2 Navigation Flow
- [ ] Setup → Host starts round → Contestants join → Game play → Results
- [ ] Back navigation safeguards (confirm before leaving active game)
- [ ] URL parameter handling for round IDs, contestant IDs

---

## Phase 8: UI/UX Polish (Week 8)

### 8.1 Styling
- [ ] Design system setup (colors, typography, spacing)
- [ ] Responsive layouts for different screen sizes
- [ ] Host interface styling (desktop-focused)
- [ ] Contestant interface styling (large display, mobile-friendly)
- [ ] Accessibility considerations (contrast, font sizes, keyboard navigation)

### 8.2 Animations & Feedback
- [ ] Letter reveal animations (Hangman)
- [ ] Clue appear transitions (Progressive)
- [ ] Timer countdown animations
- [ ] Correct/incorrect visual feedback
- [ ] Loading states and spinners

### 8.3 Sound Effects
- [ ] Add correct answer sound
- [ ] Add incorrect answer sound
- [ ] Host-triggered sound playback via WebSocket
- [ ] Volume controls and mute option

---

## Phase 9: Testing & Quality Assurance (Week 9)

### 9.1 Unit Tests
- [ ] Test question type logic
- [ ] Test game state management
- [ ] Test database operations
- [ ] Test WebSocket message handlers

### 9.2 Integration Tests
- [ ] Test host ↔ contestant synchronization
- [ ] Test full game flow (setup → play → results)
- [ ] Test reconnection scenarios
- [ ] Test multi-contestant scenarios

### 9.3 Manual Testing
- [ ] Test each question type end-to-end
- [ ] Test with multiple devices/browsers
- [ ] Test edge cases (network failures, rapid clicking, etc.)
- [ ] Cross-browser compatibility testing

---

## Phase 10: Deployment & Documentation (Week 10)

### 10.1 Deployment Setup
- [ ] Choose hosting platform (Vercel, Railway, etc.)
- [ ] Set up production database (PostgreSQL)
- [ ] Configure environment variables for production
- [ ] Set up WebSocket server for production
- [ ] Test production build locally

### 10.2 Documentation
- [ ] Write user guide for hosts
- [ ] Document question creation process
- [ ] Create troubleshooting guide
- [ ] Document deployment process
- [ ] Add code comments and API documentation

### 10.3 Launch
- [ ] Deploy to production
- [ ] Monitor performance and errors
- [ ] Gather initial user feedback
- [ ] Create issue backlog for post-launch improvements

---

## Technical Stack Summary

### Frontend
- **Svelte 5** with SvelteKit 2
- **TypeScript** (strict mode)
- **Vite 7** for bundling
- Real-time UI updates via WebSocket

### Backend
- **SvelteKit server routes** for API endpoints
- **WebSocket server** (integrated with SvelteKit)
- **PostgreSQL** for data persistence
- Connection pooling for database

### Development Tools
- **svelte-check** for type checking
- **vite-plugin-devtools-json** for dev experience
- Testing framework (Vitest or Playwright)

---

## Key Milestones

| Milestone | Target | Deliverable |
|-----------|--------|-------------|
| Database & WebSocket ready | Week 2 | Can store questions and sync basic messages |
| All question types working | Week 4 | Can play all 4 question types locally |
| Full host interface | Week 5 | Host can control entire game flow |
| Full contestant interface | Week 6 | Contestant sees live updates |
| MVP complete | Week 8 | Full game playable with all features |
| Production ready | Week 10 | Deployed and documented |

---

## Risk Mitigation

### WebSocket Reliability
- **Risk:** Connection drops during gameplay
- **Mitigation:** Implement robust reconnection with state recovery

### Database Performance
- **Risk:** Slow queries affecting real-time experience
- **Mitigation:** Index optimization, caching, connection pooling

### Time Synchronization
- **Risk:** Timer drift between host and contestants
- **Mitigation:** Server-authoritative timer, periodic sync

### Scalability
- **Risk:** Multiple simultaneous rounds/contestants
- **Mitigation:** Design stateless architecture, plan for horizontal scaling

---

## Future Enhancements (Post-MVP)

1. **Multiple simultaneous rounds** - Support concurrent games
2. **User authentication** - Secure login for hosts/contestants
3. **Game history** - View past rounds and results
4. **Image support** - Upload product images for questions
5. **Sound & music** - Background music, enhanced sound effects
6. **Mobile optimization** - Dedicated mobile contestant app
7. **Analytics** - Track question difficulty, contestant performance
8. **Question templates** - Pre-built question packs
9. **Customizable themes** - Branding and color schemes
10. **Live streaming integration** - OBS overlays for broadcasts
