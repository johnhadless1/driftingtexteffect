# driftingtexteffect
Drifting text effect with HTML, CSS and JS.

![example](https://github.com/johnhadless1/driftingtexteffect/blob/main/example.gif)

For full example/demo, download the _drifty.html_ to see the effect in action.

### ! ! ! Results may vary of use of browsers.

The example gif's page was rendered in Firefox-based browser.

Chrome (or Chromium-based browsers) is much better if you want smooth movement, but makes them slightly blurry.

> [!NOTE]
> This was been vibe coded by Claude, then fixed by local LLM gemma4.

## For easy copy-pasting:

### CSS
```
/* This is the core styling for the drifting effect */
.drift-text {
  display: inline-flex; /* Allows it to sit inside sentences */
  white-space: nowrap;
  position: relative;
  line-height: 1.4;
  /* Optional: Default styles. Remove these if you want to use your own fonts */
  font-weight: 900;
  font-family: 'Arial Black', Impact, sans-serif;
}

.drift-text .letter {
  display: inline-block;
  position: relative;
  will-change: transform; /* Optimizes performance for animations */
}
```

### JS (Just put it in <script> tag.)
```
class DriftText {
  constructor(element) {
    this.el = element;
    // Read custom settings from data attributes, or use defaults
    this.driftAmt = parseFloat(this.el.dataset.drift) || 12;
    this.rotAmt = parseFloat(this.el.dataset.rot) || 15;
    this.speed = parseFloat(this.el.dataset.speed) || 4;
    this.letters = [];
    this.init();
  }

  init() {
    const text = this.el.textContent.trim();
    // Wrap every single character in a <span>
    this.el.innerHTML = [...text].map(c => 
      `<span class="letter">${c === ' ' ? '&nbsp;' : c}</span>`
    ).join('');

    // Assign a unique random phase and frequency to every letter
    // This prevents them all moving in the exact same direction
    this.letters = [...this.el.querySelectorAll('.letter')].map(span => ({
      el: span,
      phaseX: Math.random() * Math.PI * 2,
      phaseY: Math.random() * Math.PI * 2,
      phaseR: Math.random() * Math.PI * 2,
      freqX: 0.7 + Math.random() * 0.6,
      freqY: 0.5 + Math.random() * 0.6,
      freqR: 0.4 + Math.random() * 0.5,
    }));
  }

  update(t) {
    const s = t * 0.0005 * this.speed;
    this.letters.forEach(l => {
      // The Magic: Use Sin/Cos for smooth circular/floating motion
      const x = Math.sin(s * l.freqX + l.phaseX) * this.driftAmt;
      const y = Math.cos(s * l.freqY + l.phaseY) * this.driftAmt;
      const rot = Math.sin(s * l.freqR + l.phaseR) * this.rotAmt;
      l.el.style.transform = `translate(${x.toFixed(2)}px, ${y.toFixed(2)}px) rotate(${rot.toFixed(2)}deg)`;
    });
  }
}

// Manager to run all instances in one single animation loop (Better performance)
const DriftManager = {
  instances: [],
  init() {
    document.querySelectorAll('.drift-text').forEach(el => {
      this.instances.push(new DriftText(el));
    });
    requestAnimationFrame(this.animate.bind(this));
  },
  animate(t) {
    this.instances.forEach(inst => inst.update(t));
    requestAnimationFrame(this.animate.bind(this));
  }
};

// Initialize when the page loads
window.addEventListener('DOMContentLoaded', () => DriftManager.init());
```

### HTML (example)
```
<h1 class="drift-text" data-drift="20" data-rot="30" data-speed="5">
  HELLO WORLD
</h1>

<p>
  This is a 
  <span class="drift-text" data-drift="5" data-rot="10" data-speed="2" style="font-size: 1.2em;">
    floating word
  </span> 
  inside a sentence.
</p>
```
