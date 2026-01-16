# Supermarket Sweep - Development Plan

## Project Overview
Building a real-time Supermarket Sweep question game with separate host and contestant interfaces, WebSocket synchronization, and IndexedDB persistence.

---

## Phase 1: Foundation & Setup (Week 1)

### 1.1 Database Design & Setup (Using Zero)
- [ ] Define Zero schema with tables: Questions, Rounds, Contestants, Results
- [ ] Set up table relationships (questions → rounds, contestants → results)
- [ ] Define column structures with proper types (string, boolean, number)
- [ ] Create arktype validators for type safety
- [ ] Design query helpers using `defineQueries` for Questions, Rounds, Contestants
- [ ] Create mutators for CRUD operations on all entities
- [ ] Set up seed data for development (sample questions, test rounds)
- [ ] Plan migration strategy for schema updates using Zero's version management

**Zero Schema Overview:**
```
Tables:
- questions (id, text, type, answer, options, clues, difficulty)
- rounds (id, name, questions[], createdAt, status)
- contestants (id, name, roundId, score, bonusTime)
- results (id, roundId, contestantId, answers, finalScore, timestamp)

Relationships:
- rounds.questionIds → questions table
- contestants.roundId → rounds table
- results.roundId → rounds, results.contestantId → contestants
```

### 1.2 WebSocket & Sync Infrastructure (Using Zero)
Zero provides built-in WebSocket sync without needing separate configuration:
- [ ] Understand Zero's automatic sync mechanism (no manual WebSocket setup needed)
- [ ] Set up Zero cache server for local development (runs on port 4848)
- [ ] Configure server auth/userID for multi-client scenarios
- [ ] Plan production sync backend (Zero can use Postgres via adapters)
- [ ] Test offline-first behavior with mock network interruptions
- [ ] Verify real-time sync between multiple concurrent contestants

**Zero handles:**
- Automatic bidirectional sync between host and contestants
- Optimistic updates (mutations apply immediately locally)
- Conflict resolution via CRDT semantics
- Reconnection state recovery
- Message protocol (internal to Zero)

### 1.3 Project Configuration (Zero Setup)
- [ ] Install dependencies: `@rocicorp/zero`, `zero-svelte`, `arktype`
- [ ] Create `src/schema.ts` with Zero schema definition using `createSchema`
- [ ] Create `src/mutators.ts` with mutator definitions for all operations
- [ ] Create `src/lib/zero.svelte.ts` with Z instance initialization
- [ ] Create `src/routes/api/mutate/+server.ts` for mutation endpoint
- [ ] Create `src/routes/api/sync/+server.ts` for sync endpoint
- [ ] Set environment variables: `PUBLIC_SERVER` (cache URL), optional auth
- [ ] Disable SSR in `src/routes/+layout.ts` (required for client-side-only Zero)
- [ ] Add `.env.local` for development (Zero cache config)

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

### 2.2 Game State Management (With Zero Queries)
- [ ] Create `queries.game.currentRound()` to fetch active round
- [ ] Create `queries.game.currentQuestion()` derived from round state
- [ ] Create `queries.contestants.byRound(roundId)` for real-time contestant list
- [ ] Implement timer state using Svelte `$state` (not persisted to Zero)
- [ ] Create `queries.results.byRound(roundId)` to track scores in real-time
- [ ] Implement game progression logic via mutators (nextQuestion, updateScore)
- [ ] Use Zero mutations for atomic score/time updates
- [ ] Handle game start/pause/reset via mutators

---

## Phase 3: Host Interface (Week 3)

### 3.1 Question Management (CRUD via Zero Mutators)
- [ ] Create UI component that calls `z.mutate(mutators.question.insert(...))`
- [ ] Implement edit form calling `z.mutate(mutators.question.update(...))`
- [ ] Add delete confirmation with `z.mutate(mutators.question.delete(...))`
- [ ] Create question list view using `z.createQuery(queries.question.all())`
- [ ] Use query `.data` for reactive list updates in Svelte components

### 3.2 Round Setup (Zero Queries & Mutations)
- [ ] Use `queries.question.all()` to display available questions
- [ ] Implement round creation: `z.mutate(mutators.round.create(...))`
- [ ] Add contestant registration: `z.mutate(mutators.contestant.register(...))`
- [ ] Create round status query: `queries.round.byId(roundId)`

### 3.3 Game Control Panel (Zero-Driven Real-Time Updates)
- [ ] Query active round with `queries.game.currentRound()`
- [ ] Query contestants with `queries.contestants.byRound(roundId)`
- [ ] Mutation: next question → `z.mutate(mutators.game.advanceQuestion(...))`
- [ ] Mutation: mark answer → `z.mutate(mutators.result.recordAnswer(...))`
- [ ] Mutation: update score → `z.mutate(mutators.contestant.updateScore(...))`
- [ ] Real-time sync: all connected clients see updates instantly via Zero's sync
- [ ] Host display uses queries that reactively update as contestants answer

### 3.4 Host Dashboard
- [ ] Query: `queries.game.currentRound()` - live round state
- [ ] Query: `queries.contestants.byRound(roundId)` - reactive contestant list
- [ ] Query: `queries.results.byRound(roundId)` - live score tracking
- [ ] All components auto-update via Zero's reactive `.data` property

---

## Phase 4: Contestant Interface (Week 4)

### 4.1 Question Display Components
- [ ] Create hangman-style letter reveal component
- [ ] Build progressive clue display component
- [ ] Create fill-in-the-blank display component
- [ ] Build price guess product display component
- [ ] Ensure large, readable fonts and clear layout

### 4.2 Game Status Display (Reactive Zero Queries)
- [ ] Build timer component (local `$state`, not in Zero)
- [ ] Score display: `queries.results.byContestant(contestantId)`
- [ ] Question indicator: derived from `queries.game.currentRound()`.questionIndex
- [ ] Connection status: use `z.connectionState` (Zero provides this natively)

### 4.3 Contestant-Specific Features
- [ ] Display contestant name/identifier
- [ ] Show correct/incorrect feedback (sound + visual)
- [ ] Handle connection status indicator
- [ ] Implement waiting/standby screen

---

## Phase 5: Real-time Synchronization (Built-in Zero)

Zero handles all real-time sync automatically—no manual WebSocket event handlers needed.

### 5.1 Automatic State Sync (Zero's CRDT Engine)
- [ ] Host mutations (e.g., `nextQuestion`) sync to all connected clients instantly
- [ ] Contestant data updates (answers, scores) sync back to host in real-time
- [ ] Test: Multiple browser windows as host/contestant—verify live sync
- [ ] Verify: Late-joining contestants receive full round state on first sync
- [ ] Test: Network interruption → automatic reconnection + state recovery

### 5.2 Zero Connection Management
- [ ] Monitor `z.connectionState` in UI (connecting → connected/disconnected)
- [ ] Implement reconnection UI: show "Connecting..." during outages
- [ ] Use `z.connection.connect()` for manual auth retry if needed
- [ ] Verify: Offline changes queue locally, sync when reconnected (CRDT)

### 5.3 Testing & Reliability
- [ ] Multi-client sync: Open 3+ browser windows, verify all see updates
- [ ] Offline resilience: Disconnect network mid-game, verify app doesn't crash
- [ ] Reconnection: Restore network, verify all changes sync correctly
- [ ] Load test: Verify sync performance with 20+ concurrent contestants

---

## Phase 6: Client-Side Data Management (Zero Queries & Local-First)

### 6.1 Zero Query Setup
- [ ] Define `queries.question.all()` for question library
- [ ] Define `queries.round.byId(id)` for round details
- [ ] Define `queries.contestants.byRound(roundId)` for contestant list
- [ ] Define `queries.results.byContestant(contestantId)` for individual scores
- [ ] Create validators using arktype for all query inputs
- [ ] All queries auto-sync with local cache; no explicit fetch needed

### 6.2 Data Persistence (Automatic via Zero)
- [ ] Zero automatically persists data to IndexedDB on client
- [ ] Questions synced server-side (via Zero cache server/Postgres)
- [ ] Rounds and contestant data synced automatically
- [ ] Results persisted locally and synced when connection available
- [ ] No manual IndexedDB code needed (Zero handles it)

### 6.3 Data Loading (Lazy via Queries)
- [ ] On app start: `z.createQuery(queries.question.all())` loads from cache
- [ ] First load uses cached IndexedDB data instantly
- [ ] Background sync with server happens automatically
- [ ] Contestants auto-load from `queries.contestants.byRound(roundId)`
- [ ] Results streamed in real-time as answers are recorded

---

## Phase 7: Routing & Navigation (Week 7)

### 7.1 Page Routes
- [ ] `/` - Landing page with role selection (host/contestant)
- [ ] `/questions` - Question library (CRUD via Zero mutators)
- [ ] `/rounds` - Round management (create, select, delete)
- [ ] `/rounds/[id]/setup` - Pre-game setup (select questions, register contestants)
- [ ] `/rounds/[id]/host` - Host control interface (real-time via Zero queries)
- [ ] `/rounds/[id]/contestant/[contestantId]` - Contestant display
- [ ] Ensure SSR disabled in root `+layout.ts` (required for Zero client-only mode)

### 7.2 Navigation Flow
- [ ] Landing → Create/Join Round → Setup → Host Starts → Contestant Views → Results
- [ ] Back navigation safeguards: Warn if leaving active game
- [ ] URL parameters: `roundId`, `contestantId` passed in routes
- [ ] Use SvelteKit's `+page.ts` to load initial Zero queries via preload

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
- [ ] Configure environment variables for production
- [ ] Set up WebSocket server for production
- [ ] Test production build locally
- [ ] Verify IndexedDB functionality in production environment

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
- **zero-svelte** for reactive Zero queries
- **Zero** for local-first sync (replaces manual WebSocket)
- **arktype** for schema validation

### Backend / Sync Layer
- **Zero Cache Server** (local dev on port 4848)
- **Production:** Zero adapter for Postgres (via `@rocicorp/zero/server/adapters/pg`)
- **SvelteKit API endpoints:**
  - `POST /api/mutate` - Processes mutations
  - `POST /api/sync` - Handles sync protocol
  - These endpoints authenticate and route to Zero handlers

### Key Zero Architecture
- **CRDT-based sync**: Offline changes merge automatically when reconnected
- **Reactive queries**: Use Svelte 5 runes (`$state`, `$derived`) for reactivity
- **IndexedDB cache**: Zero manages local persistence automatically
- **Optimistic updates**: Mutations apply locally immediately, sync in background
- **No manual WebSocket code**: Zero abstracts all network communication

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

## Zero Integration Guide

### Installation & Setup Checklist
```bash
npm install @rocicorp/zero zero-svelte arktype
```

### Core Files to Create

#### 1. `src/schema.ts` - Zero Schema Definition
```typescript
import { createBuilder, createSchema, defineQueries, defineQuery, table, string, boolean, number, relationships } from '@rocicorp/zero';
import { type } from 'arktype';

// Define tables
const questions = table('question')
  .columns({ id: string(), text: string(), type: string(), answer: string(), difficulty: number() })
  .primaryKey('id');

const rounds = table('round')
  .columns({ id: string(), name: string(), status: string(), createdAt: number() })
  .primaryKey('id');

const contestants = table('contestant')
  .columns({ id: string(), name: string(), roundId: string(), score: number() })
  .primaryKey('id');

const results = table('result')
  .columns({ id: string(), roundId: string(), contestantId: string(), answers: string(), timestamp: number() })
  .primaryKey('id');

export const schema = createSchema({
  tables: [questions, rounds, contestants, results],
  relationships: [
    // roundQuestions, etc.
  ]
});

// Define reusable queries with validators
export const queries = defineQueries({
  question: {
    all: defineQuery(() => zql.question),
    byId: defineQuery(type({ id: 'string' }), ({ args: { id } }) => zql.question.where('id', '=', id))
  },
  round: {
    byId: defineQuery(type({ id: 'string' }), ({ args: { id } }) => zql.round.where('id', '=', id))
  },
  contestants: {
    byRound: defineQuery(type({ roundId: 'string' }), ({ args: { roundId } }) => zql.contestant.where('roundId', '=', roundId))
  }
});

declare module '@rocicorp/zero' {
  interface DefaultTypes {
    schema: typeof schema;
  }
}
```

#### 2. `src/mutators.ts` - Mutations for CRUD Operations
```typescript
import { defineMutators, defineMutator } from '@rocicorp/zero';

export const mutators = defineMutators({
  question: {
    insert: defineMutator<{ id: string; text: string; type: string; answer: string; difficulty: number }>(
      async ({ tx, args }) => {
        await tx.mutate.question.insert(args);
      }
    ),
    update: defineMutator<{ id: string; text?: string; answer?: string }>(
      async ({ tx, args: { id, ...updates } }) => {
        await tx.mutate.question.update({ id, ...updates });
      }
    ),
    delete: defineMutator<{ id: string }>(
      async ({ tx, args: { id } }) => {
        await tx.mutate.question.delete({ id });
      }
    )
  },
  // Similar patterns for round, contestant, result mutators
});
```

#### 3. `src/lib/zero.svelte.ts` - Z Instance Initialization
```typescript
import { PUBLIC_SERVER } from '$env/static/public';
import { Z } from 'zero-svelte';
import { schema } from '../schema.js';
import { mutators } from '../mutators.js';

export const z = new Z({
  cacheURL: PUBLIC_SERVER,
  schema,
  mutators,
  userID: 'anon', // or authenticated user ID
  kvStore: 'idb'  // IndexedDB for persistence
});

export { mutators };
```

#### 4. `src/routes/+layout.ts` - Disable SSR
```typescript
export const ssr = false; // Required: Zero is client-only
```

#### 5. `src/routes/api/mutate/+server.ts` - Mutation Endpoint
```typescript
import { handleMutateRequest } from '@rocicorp/zero/server';
// Handles mutation requests from Z instance
```

#### 6. `src/routes/api/sync/+server.ts` - Sync Endpoint
```typescript
import { handleSyncRequest } from '@rocicorp/zero/server';
// Handles sync protocol for bidirectional data sync
```

#### 7. `.env.local` - Development Configuration
```
PUBLIC_SERVER=http://localhost:4848
```

### Usage Patterns in Components

#### Create a Query
```svelte
<script lang="ts">
  import { z, mutators } from '$lib/zero.svelte';
  import { queries } from '../schema';
  
  const questions = z.createQuery(queries.question.all());
</script>

{#each questions.data as q (q.id)}
  <div>{q.text}</div>
{/each}
```

#### Perform a Mutation
```svelte
<script lang="ts">
  import { z, mutators } from '$lib/zero.svelte';
  
  function addQuestion(text: string) {
    z.mutate(mutators.question.insert({
      id: crypto.randomUUID(),
      text,
      type: 'fillBlank',
      answer: 'test',
      difficulty: 1
    }));
  }
</script>

<button onclick={() => addQuestion('New Q')}>Add</button>
```

#### Reactive Queries with $derived
```svelte
<script lang="ts">
  let roundId = $state('round-1');
  const contestants = $derived(z.createQuery(queries.contestants.byRound({ roundId })));
</script>
```

#### Monitor Connection
```svelte
<script lang="ts">
  import { z } from '$lib/zero.svelte';
</script>

{#if z.connectionState.name === 'connected'}
  <span>✓ Connected</span>
{:else if z.connectionState.name === 'connecting'}
  <span>⟳ Connecting...</span>
{/if}
```

### Development Workflow

1. **Local Development:**
   - Run Zero cache server: `zero-cache` (port 4848)
   - Run SvelteKit dev: `npm run dev`
   - App syncs with cache server automatically

2. **Multi-Client Testing:**
   - Open multiple browser windows/tabs
   - Changes in one instantly sync to others
   - Simulate offline by disabling network in DevTools

3. **Production Deployment:**
   - Configure Zero with Postgres adapter
   - Set `PUBLIC_SERVER` to production sync URL
   - Deploy SvelteKit app alongside Zero sync server

### Key Advantages of Zero

✅ **Automatic sync** - No manual WebSocket code  
✅ **Offline-first** - Changes queue locally, sync when reconnected  
✅ **Type-safe** - Full TypeScript support via schema definitions  
✅ **Real-time** - CRDTs handle concurrent edits automatically  
✅ **Optimistic updates** - Mutations appear instantly, resolve in background  
✅ **Reactive** - Svelte 5 runes make UI update automatically  

### References

- [Zero Documentation](https://zero.rocicorp.dev/docs/introduction)
- [zero-svelte GitHub](https://github.com/stolinski/zero-svelte)
- [Zero Sync Video](https://www.youtube.com/watch?v=hAxdOUgjctk&ab_channel=Syntax)

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
