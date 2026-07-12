# 🌿 Firefly Synchronization — Interactive Simulation

An interactive, browser-based simulation of firefly synchronization using two
classical mathematical models from the synchronization literature.

## 🔬 Scientific Background

### Why do fireflies synchronize?

In parts of Southeast Asia and the eastern United States, thousands of
fireflies flash *in perfect unison* — producing a pulsing collective
light display with no central coordinator. Each firefly adjusts its
own flash timing based on what it sees around it, and synchrony
emerges spontaneously from these local interactions.

This is a canonical example of **self-organized collective behavior**
in biology — the same class of phenomenon seen in cardiac pacemaker
cells, neural oscillations, and circadian rhythms.

The key ecological questions:
- How strong does the coupling between individuals need to be?
- Does synchrony break down with increasing environmental noise?
- How do heterogeneous flash frequencies affect collective synchrony?
- Do different species use different coupling mechanisms?

Two mathematical frameworks capture this behavior at different levels
of biological realism.

---

## 📐 Model 1 — Kuramoto Phase Oscillators

### Reference
> Kuramoto, Y. (1984). *Chemical Oscillations, Waves, and Turbulence.*
> Springer-Verlag, Berlin.

### Biological interpretation

Each firefly is modeled as a **phase oscillator** — a clock that
continuously advances through a cycle [0, 2π). When the phase
crosses 2π, the firefly flashes and resets to 0. The coupling term
means each firefly is gently "pulled" toward the mean phase of the
population on every timestep.

### Equation

```
dθᵢ/dt = ωᵢ + (K/N) Σⱼ sin(θⱼ − θᵢ) + η(t)
```

| Symbol | Meaning |
|--------|---------|
| θᵢ ∈ [0, 2π) | Phase of firefly i |
| ωᵢ ~ 𝒩(ω₀, σ²) | Natural flash frequency (Gaussian spread) |
| K | Global coupling strength |
| N | Population size |
| η(t) ~ 𝒩(0, D) | Stochastic noise (environmental variability) |

### Key result: phase transition at Kc

The Kuramoto model exhibits a **second-order phase transition**:

```
Kc = 2σ√(2/π)    (critical coupling for Gaussian frequency distribution)
```

- K < Kc : fireflies flash independently (r ≈ 0, disordered)
- K > Kc : spontaneous synchrony emerges (r → 1)
- Higher σ (more diverse flash frequencies) → harder to synchronize → higher Kc

### Order parameter

```
r · e^{iψ} = (1/N) Σⱼ e^{iθⱼ}
```

- **r ∈ [0, 1]**: measures coherence; r = 0 (random), r = 1 (fully synchronized)
- **ψ**: mean phase of the population

---

## ⚡ Model 2 — Mirollo-Strogatz Integrate-and-Fire

### Reference
> Mirollo, R.E. & Strogatz, S.H. (1990). Synchronization of pulse-coupled
> biological oscillators. *SIAM Journal on Applied Mathematics,* 50(6): 1645–1662.

### Biological interpretation

This model is closer to the actual mechanism of firefly flashing.
Each firefly accumulates an internal **potential x ∈ [0, 1]** (analogous
to a neuron charging toward its firing threshold).

When potential reaches 1 → the firefly **fires** (flashes) and instantly
resets to 0. The flash sends a pulse to all other fireflies, nudging
their potential forward. If any nudged firefly is pushed past 1, it
also fires — a **cascade fire** — propagating synchrony.

### Equations

**Rise phase:**
```
ẋᵢ = ωᵢ    (potential accumulates at natural rate)
```

**Firing rule** (when xᵢ reaches 1):
```
xᵢ → 0              (reset)
xⱼ → f⁻¹(f(xⱼ) + ε)   for all j ≠ i   (nudge neighbors)
```

**Concave rise function:**
```
f(x)   = ln(1 + (eᵇ − 1)·x) / b       [0,1] → [0,1]
f⁻¹(y) = (e^(by) − 1) / (eᵇ − 1)
```

| Symbol | Meaning |
|--------|---------|
| xᵢ ∈ [0, 1) | Internal potential of firefly i |
| ωᵢ | Natural charging rate (nearly identical) |
| ε | Activation strength (pulse size per firing) |
| b > 0 | Concavity parameter of rise function |

### Key result: guaranteed synchrony (absorption)

**Theorem (Mirollo & Strogatz 1990):** For N identical oscillators with
a concave rise function and any ε > 0, starting from *any* initial
conditions (except a set of measure zero), the system will reach full
synchrony in finite time.

The concavity of f is essential: it ensures that the **time saved** by a
nudge always exceeds the separation, so oscillators perpetually converge.

### Cascade behavior

When synchrony is near-complete, a single firing can nudge many
near-threshold fireflies past 1 simultaneously — producing a **cascade
fire** where the whole population flashes at once. This is the visual
spectacle seen in Southeast Asian mangrove forests.

---

## 📊 Model Comparison

| Property | Kuramoto | Mirollo-Strogatz |
|---|---|---|
| State variable | Phase θ ∈ [0, 2π) | Potential x ∈ [0, 1) |
| Coupling type | Continuous sinusoidal | Discrete pulse on firing |
| Sync condition | K > Kc (sharp transition) | Any ε > 0 (guaranteed) |
| Biological basis | Phase-coupling model | Integrate-and-fire neuron |
| Heterogeneity | Tolerates σ spread | Requires near-identical ωᵢ |
| Flash type | Smooth periodic glow | Sharp discrete pulses |
| Cascade effects | None | Yes — cascade firing |
| Best models | Gradual synchrony | Cascade wave synchrony |

---

## 🎮 Controls

### Shared (both models)
| Control | Effect |
|---------|--------|
| **N** (Fireflies) | Population size. Larger N → smoother mean-field |
| **Noise D** | Environmental stochasticity. High noise breaks synchrony |
| **Flash width** | Visual glow duration (Kuramoto display only) |
| **⚡ Scramble** | Randomize all phases — watch synchrony re-emerge |
| **↺ New swarm** | Reinitialize with fresh random positions |

### Kuramoto-specific
| Control | Effect |
|---------|--------|
| **K** (Coupling) | Main synchrony knob. Raise past Kc to synchronize |
| **σ** (Frequency spread) | Diversity of natural frequencies. Sets Kc |

### Mirollo-Strogatz-specific
| Control | Effect |
|---------|--------|
| **ε** (Activation) | Pulse strength per firing. Larger → faster sync |
| **ω₀** (Base frequency) | Natural charging rate |
| **b** (Concavity) | Rise function curvature. Higher → stronger absorption |

---

## 📈 Visualizations

### 1. Forest canvas
Real-time animation of N fireflies against a dark forest background.
- **Kuramoto**: smooth yellow-green glow varies continuously with phase
- **Mirollo-Strogatz**: sharp amber flashes; cascades appear as waves

Screen-blend compositing creates additive glow — when fully synchronized,
overlapping flashes produce a collective brightening of the scene.

### 2. Phase wheel (unit circle)
Each firefly is a dot on the unit circle at its current phase angle.
- **Random**: dots evenly distributed around the circle
- **Synchronizing**: dots cluster into one or a few groups
- **Fully synced**: all dots at a single point, moving together

The yellow/amber arrow is the **order parameter vector** r·e^{iψ} — its
length is r (the synchrony measure) and its angle is the mean phase ψ.

### 3. r(t) synchrony chart
Time series of the order parameter r over the last 300 timesteps.
Watch for:
- A jump from r ≈ 0 to r ≈ 1 when K crosses Kc (Kuramoto)
- Rapid rise followed by sustained r ≈ 1 (Mirollo-Strogatz)
- Oscillations around intermediate r when K ≈ Kc or noise is added

---

## 🧮 Implementation Notes

### Numerical integration
Both models use **forward Euler** with timestep dt = 0.022 simulation
seconds. This is sufficient for the parameter regimes of interest, though
higher-order methods (RK4) could be used for more accurate transient dynamics.

Stochastic noise is added as a **Langevin term** (multiplicative by √dt)
following the Itô convention for stochastic differential equations.

### Synchronous update
Both models use a **snapshot of current states** before updating — all
oscillators advance simultaneously rather than sequentially. This avoids
order-dependence artifacts and matches the mean-field theory assumptions.

### Cascade handling (MS model)
The cascade is processed in two levels:
1. **Level 0**: All oscillators that naturally cross x = 1 in a timestep fire
2. **Level 1**: Pulses from all Level 0 firers are summed and applied to remaining oscillators; any that cross 1 fire in cascade
3. **Level 2**: Secondary cascade from Level 1 firers applied to remaining

This is a reasonable approximation of the continuous cascade. Full cascade
computation would require iterative processing until no new crossings occur.

### Performance optimization
Firefly glows are rendered using pre-computed radial gradient **sprites**
drawn with `ctx.drawImage()` rather than re-creating `createRadialGradient()`
each frame — approximately 3× faster for N = 100–200.

Screen-blend compositing (`globalCompositeOperation = 'screen'`) is used
for the glow layer, making simultaneous flashes produce additive brightness
— physically accurate for coherent light sources.


## 📚 References

1. **Kuramoto, Y.** (1984). *Chemical Oscillations, Waves, and Turbulence.* Springer-Verlag.

2. **Mirollo, R.E. & Strogatz, S.H.** (1990). Synchronization of pulse-coupled biological oscillators. *SIAM Journal on Applied Mathematics,* 50(6): 1645–1662.

3. **Buck, J.** (1988). Synchronous rhythmic flashing of fireflies. *Quarterly Review of Biology,* 63(3): 265–289.

4. **Strogatz, S.H.** (2000). From Kuramoto to Crawford: exploring the onset of synchronization in populations of coupled oscillators. *Physica D: Nonlinear Phenomena,* 143(1): 1–20.

5. **Lewis, S.M. et al.** (2024). Illuminating firefly diversity: trends, threats and conservation strategies. *Insects,* 15(1): 71. [open access]

6. **Strogatz, S.H.** (2003). *Sync: The Emerging Science of Spontaneous Order.* Hyperion Press. *(accessible introduction)*


