# Re-rendering optimization strategies

## Using props to avoid re-renders

### In this code
- `children` are **stable references** because `App` does not re-render.  
- React compares by reference and sees no change.  
- Only `MovingBlock` updates, avoiding wasted renders of heavy components.


```typescript
import { ReactNode, useState } from 'react';

import { BunchOfStuff, OtherStuffAlsoComplicated } from './components/mocks';
import { VerySlowComponent } from './components/very-slow-component';
import './styles.scss';

const MovingBlock = ({ position }: { position: number }) => (
  <div className="movable-block" style={{ top: position }}>
    {position}
  </div>
);

// just hard-coded approximation to demonstrate the re-renders problem
// not to be used in real code
const getPosition = (val: number) => 150 - val / 2;

const ScrollableWithMovingBlock = ({ children }: { children: ReactNode }) => {
  const [position, setPosition] = useState(150);

  const onScroll = (e: any) => {
    const calculated = getPosition(e.target.scrollTop);
    setPosition(calculated);
  };

  return (
    <div className="scrollable-block" onScroll={onScroll}>
      <MovingBlock position={position} />
      {/* put our children prop here, where the slow bunch of stuff used to be */}
      {children}
    </div>
  );
};

export default function App() {
  return (
    <ScrollableWithMovingBlock>
      <VerySlowComponent />
      <BunchOfStuff />
      <OtherStuffAlsoComplicated />
    </ScrollableWithMovingBlock>
  );
}

```

### Why this React code is optimized

The optimization comes from **isolating stateful updates** into a small component instead of mixing them with slow components.

### Breakdown
1. **State location**  
   - `ScrollableWithMovingBlock` owns the `position` state.  
   - When you scroll, only this component re-renders.

2. **Children stability**  
   - `VerySlowComponent`, `BunchOfStuff`, and `OtherStuffAlsoComplicated` are passed as `children`.  
   - `App` does not re-render when `position` changes, so the `children` reference stays **the same**.  
   - React can skip reconciling those components because their props have not changed.

3. **MovingBlock isolation**  
   - Only `MovingBlock` receives the changing prop `position`.  
   - It re-renders, while slow components do not.

---