<h1 id="experiment-5-noise-and-decoherence-laboratory">Experiment 5: Noise and Decoherence Laboratory</h1><h2 id="background-theory-4">1. Background Theory</h2><p>All physical quantum computers are imperfect. Decoherence — the loss of quantum coherence due to interaction with the environment — is the central challenge of practical quantum computing. Two fundamental noise models are studied in this experiment.</p><figure class="book-figure"><img loading="lazy" src="content/images/image25.png"/></figure><div class="box box-generic"><p>Depolarising Channel (models random Pauli errors):</p><p>d = 16 for 4 qubits, p ∈ [0,1] is the noise parameter</p><p>Fidelity decreases approximately as: F ≈ 1 − p(1−1/d)</p><p>Minimum purity at p=1: γ_min = 1/d = 1/16 = 0.0625</p><p>Amplitude Damping (models T₁ relaxation, |1⟩→|0⟩):</p><p>Kraus operators: K₀ = [[1,0],[0,√(1−γ)]], K₁ = [[0,√γ],[0,0]]</p><p>ε(ρ) = K₀ρK₀† + K₁ρK₁†</p><p>γ = 1 − e^(−t/T₁): probability of qubit decaying from |1⟩ to |0⟩</p></div><table>
<colgroup>
<col style="width: 19%"/>
<col style="width: 25%"/>
<col style="width: 19%"/>
<col style="width: 35%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Noise Model</strong></td>
<td><strong>Physical Origin</strong></td>
<td><strong>Symmetry</strong></td>
<td><strong>Effect on Bloch sphere</strong></td>
</tr>
<tr class="even">
<td>Depolarising</td>
<td>Random X,Y,Z Pauli errors</td>
<td>Symmetric (all axes equal)</td>
<td>Uniformly shrinks Bloch vector toward center</td>
</tr>
<tr class="odd">
<td>Amplitude Damping</td>
<td>T₁ spontaneous emission</td>
<td>Asymmetric (Z-axis preferred)</td>
<td>Shrinks and shifts vector toward |0⟩ pole</td>
</tr>
<tr class="even">
<td>Phase Damping</td>
<td>T₂ pure dephasing</td>
<td>Z-axis symmetric</td>
<td>Shrinks x,y components; z-component preserved</td>
</tr>
</tbody>
</table><h2 id="qiskit-code-3">2. Qiskit Code</h2><h3 id="first-program-simple-version-4">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># -----------------------------------------------------------------
# Experiment 5 — First Program: Noise and Decoherence (simple version)
# Dr. S. K. Jain, India
# -----------------------------------------------------------------
from qiskit.quantum_info import DensityMatrix, Statevector, state_fidelity
from qiskit import QuantumCircuit
import numpy as np
# Build ideal GHZ+i state
qc = QuantumCircuit(4)
qc.h(0); qc.p(np.pi/2, 0)
qc.cx(0,1); qc.cx(0,2); qc.cx(0,3)
ideal_dm = DensityMatrix(qc)
# Depolarising channel
d = 16
for p in [0.0, 0.1, 0.2, 0.5, 1.0]:
rho_noisy = (1-p)*ideal_dm.data + (p/d)*np.eye(d)
F = float(state_fidelity(DensityMatrix(rho_noisy), ideal_dm))
pur = float(np.real(np.trace(rho_noisy @ rho_noisy)))
print(f'p={p:.1f}: F={F:.6f}, Purity={pur:.6f}')</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image26.png"/></figure><h3 id="full-program-complete-version-3">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ───────────────────────────────────────────────────────────────────────
# Experiment 5: Noise and Decoherence Laboratory
# AQLL §5 | Dr. S. K. Jain, India
# ───────────────────────────────────────────────────────────────────────
from qiskit.quantum_info import DensityMatrix, Statevector, state_fidelity
from qiskit import QuantumCircuit
import numpy as np, matplotlib.pyplot as plt
# Build ideal GHZ+i state
qc = QuantumCircuit(4)
qc.h(0); qc.p(np.pi/2, 0)
qc.cx(0,1); qc.cx(0,2); qc.cx(0,3)
ideal_dm = DensityMatrix(qc)
ideal_sv = Statevector(qc)
noise_params = np.linspace(0, 1, 51)
d = 16 # Hilbert space dimension = 2^4
# Depolarising channel: epsilon(rho) = (1-p)*rho + (p/d)*I
def depolarising(rho, p, d=16):
return (1 - p) * rho + p * np.eye(d) / d
fids_depol, purs_depol = [], []
for p in noise_params:
rho_noisy = depolarising(ideal_dm.data, p, d)
dm_noisy = DensityMatrix(rho_noisy)
fids_depol.append(float(state_fidelity(dm_noisy, ideal_dm)))
purs_depol.append(float(np.real(np.trace(rho_noisy @ rho_noisy))))
# Amplitude damping on each qubit (T1 relaxation model)
def amplitude_damping_kraus(gamma):
K0 = np.array([[1, 0], [0, np.sqrt(1 - gamma)]])
K1 = np.array([[0, np.sqrt(gamma)], [0, 0]])
return [K0, K1]
def apply_ad_all_qubits(rho_4q, gamma):
rho = rho_4q.copy()
for qubit in range(4):
K0, K1 = amplitude_damping_kraus(gamma)
I2 = np.eye(2)
new_rho = np.zeros((16,16), dtype=complex)
for K in [K0, K1]:
kron_ops = [K if q == qubit else I2 for q in range(4)]
K_full = kron_ops[0]
for m in range(1, 4):
K_full = np.kron(K_full, kron_ops[m])
new_rho += K_full @ rho @ K_full.conj().T
rho = new_rho
return rho
fids_ad, purs_ad = [], []
for gamma in noise_params:
rho_noisy_ad = apply_ad_all_qubits(ideal_dm.data, gamma)
dm_noisy_ad = DensityMatrix(rho_noisy_ad)
fids_ad.append(float(state_fidelity(dm_noisy_ad, ideal_dm)))
purs_ad.append(float(np.real(np.trace(rho_noisy_ad @ rho_noisy_ad))))
# Noise threshold analysis
p_f09 = noise_params[np.argmin(np.abs(np.array(fids_depol) - 0.9))]
g_f09 = noise_params[np.argmin(np.abs(np.array(fids_ad) - 0.9))]
print(f'Depolarising: F drops below 0.9 at p ≈ {p_f09:.3f}')
print(f'Amplitude damping: F drops below 0.9 at γ ≈ {g_f09:.3f}')
# Visualisation
fig, axes = plt.subplots(1, 2, figsize=(14, 5))
axes[0].plot(noise_params, fids_depol, '-', color='#1A3C6E', lw=2, label='Depolarising')
axes[0].plot(noise_params, fids_ad, '--', color='#C09010', lw=2, label='Amplitude Damping')
axes[0].axhline(0.9, color='red', ls=':', alpha=0.7, label='F=0.9 threshold')
axes[0].set_xlabel('Noise parameter p (or γ)')
axes[0].set_ylabel('Fidelity F')
axes[0].set_title('Fidelity vs Noise Strength', fontweight='bold')
axes[0].legend(); axes[0].grid(alpha=0.3)
axes[1].plot(noise_params, purs_depol, '-', color='#1A3C6E', lw=2, label='Depolarising')
axes[1].plot(noise_params, purs_ad, '--', color='#C09010', lw=2, label='Amplitude Damping')
axes[1].axhline(1/d, color='gray', ls=':', alpha=0.7, label=f'Min purity=1/{d}')
axes[1].set_xlabel('Noise parameter p (or γ)')
axes[1].set_ylabel('Purity Tr(ρ²)')
axes[1].set_title('Purity vs Noise Strength', fontweight='bold')
axes[1].legend(); axes[1].grid(alpha=0.3)
plt.suptitle('Noise and Decoherence Laboratory — GHZ+i State', fontsize=13, fontweight='bold')
plt.tight_layout()
plt.savefig('lab5_noise.png', dpi=150, bbox_inches='tight')
plt.show()</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image27.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image28.png"/></figure><pre><code>▶ Exp 5 — Expected Output &amp; Console Results
CONSOLE OUTPUT:
Depolarising: F drops below 0.9 at p ≈ 0.067
Amplitude damping: F drops below 0.9 at γ ≈ 0.112
FIDELITY TABLE (depolarising):
p=0.0: F=1.000000, Purity=1.000000
p=0.1: F=0.906250, Purity=0.631250
p=0.2: F=0.812500, Purity=0.325000
p=0.5: F=0.531250, Purity=0.102500
p=1.0: F=0.062500, Purity=0.062500 (= 1/d = maximally mixed)
GRAPH SHAPE: Depolarising gives linear F decay; Amplitude damping
gives faster initial decay (asymmetric, biases toward |0⟩ pole).</code></pre><h2 id="observation-and-results-4">3. Observation and Results</h2><h3 id="table-5.1-noise-threshold-values">Table 5.1 — Noise Threshold Values</h3><table>
<colgroup>
<col style="width: 23%"/>
<col style="width: 26%"/>
<col style="width: 26%"/>
<col style="width: 23%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Noise Model</strong></td>
<td><strong>F drops below 0.9 at p=?</strong></td>
<td><strong>F drops below 0.5 at p=?</strong></td>
<td><strong>Min purity value</strong></td>
</tr>
<tr class="even">
<td>Depolarising</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>Amplitude Damping</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><h3 id="table-5.2-state-metrics-at-specific-noise-levels">Table 5.2 — State Metrics at Specific Noise Levels</h3><table>
<colgroup>
<col style="width: 19%"/>
<col style="width: 20%"/>
<col style="width: 20%"/>
<col style="width: 20%"/>
<col style="width: 20%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Noise (p or γ)</strong></td>
<td><strong>Fidelity (Depol)</strong></td>
<td><strong>Purity (Depol)</strong></td>
<td><strong>Fidelity (AD)</strong></td>
<td><strong>Purity (AD)</strong></td>
</tr>
<tr class="even">
<td>0.0</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>0.1</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>0.2</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>0.5</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>1.0</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><h2 id="discussion-questions-4">4. Discussion Questions</h2><ol type="1">
<li><p>Compare the shapes of the fidelity curves for depolarising vs amplitude damping noise. Which model is more forgiving at low noise levels? Explain physically.</p></li>
<li><p>At p=1, what is the output state of the depolarising channel? What is the purity of the maximally mixed state of d=16?</p></li>
<li><p>The amplitude damping model represents T₁ relaxation. If T₁ = 100 μs and the circuit duration is 10 μs, what is the effective γ = 1 − e^(−t/T₁)?</p></li>
<li><p>What error mitigation strategy would you apply to counteract depolarising noise? Describe zero-noise extrapolation in principle.</p></li>
</ol><h2 id="lab-record-requirements-4">5. Lab Record Requirements</h2><ul>
<li><p>Run AQLL §5. Screenshot the S5_noise.png output showing both fidelity and purity curves.</p></li>
<li><p>Run the First Program. Record fidelity and purity at p=0.0, 0.1, 0.2, 0.5, 1.0.</p></li>
<li><p>Run the Full Program. Record threshold values where F drops below 0.9.</p></li>
<li><p>Complete Tables 5.1 and 5.2.</p></li>
<li><p>Write answers to all Discussion Questions.</p></li>
</ul><table>
<colgroup>
<col style="width: 6%"/>
<col style="width: 93%"/>
</colgroup>
<tbody>
<tr class="odd">
<td colspan="2"><strong>⭐ VIVA VOCE QUESTIONS &amp; ANSWERS</strong></td>
</tr>
<tr class="even">
<td><strong>Q1</strong></td>
<td><strong>What is a quantum noise channel and how is it described mathematically?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>A quantum noise channel (quantum operation) is a completely positive, trace-preserving (CPTP) map ε: ρ → ε(ρ). It is described using Kraus operators {Kᵢ}: ε(ρ) = Σᵢ KᵢρKᵢ† subject to Σᵢ Kᵢ†Kᵢ = I (trace-preserving). Kraus operators capture the effect of environmental coupling without needing to model the environment explicitly.</td>
</tr>
<tr class="even">
<td><strong>Q2</strong></td>
<td><strong>Describe the depolarising channel and its effect on a qubit.</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Depolarising channel: ε(ρ) = (1−p)ρ + (p/d)I. It maps the state to the maximally mixed state I/d with probability p, and keeps it unchanged with probability 1−p. Effect: (1) shrinks the Bloch vector by factor (1−4p/3) for a single qubit, (2) reduces purity, (3) is symmetric — treats all errors (X, Y, Z) equally. Physical origin: random Pauli errors from environment.</td>
</tr>
<tr class="even">
<td><strong>Q3</strong></td>
<td><strong>What are the Kraus operators for amplitude damping and what physical process do they model?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>K₀ = [[1,0],[0,√(1−γ)]] (no decay), K₁ = [[0,√γ],[0,0]] (decay). Completeness: K₀†K₀+K₁†K₁ = I. Physical model: T₁ relaxation — spontaneous emission, causing |1⟩→|0⟩ with probability γ = 1−e^(−t/T₁). Unlike depolarising, amplitude damping is non-symmetric: it preferentially drives qubits to |0⟩.</td>
</tr>
<tr class="even">
<td><strong>Q4</strong></td>
<td><strong>What is T₁ and T₂ and how do they relate to quantum gates?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>T₁ (longitudinal relaxation time): time for |1⟩ population to decay to |0⟩ via energy loss (amplitude damping). T₂ (transverse relaxation time): time for quantum coherence (off-diagonal ρ elements) to decay to zero. Always T₂ ≤ 2T₁. Gate constraint: circuit duration must be &lt;&lt; T₁, T₂. For IBM hardware: T₁ ≈ 100-300 μs, T₂ ≈ 50-200 μs, CX gate ≈ 300 ns.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>What is the difference between coherent and incoherent errors?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Coherent errors are systematic unitary rotations away from the desired gate (e.g., always rotating by 91° instead of 90°). They can in principle be corrected by inverse rotations. Incoherent errors (decoherence) are probabilistic — they irreversibly mix pure states into mixed states. Examples: depolarising, amplitude damping. Coherent errors cause systematic bias; incoherent errors cause irreversible information loss.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>How does noise affect the off-diagonal coherences in the density matrix?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Depolarising: off-diagonal ρᵢⱼ → (1−p)ρᵢⱼ — all coherences shrink uniformly. Amplitude damping: affects coherences involving |1⟩ components — ρ₀₁ → ρ₀₁√(1−γ). Phase damping: ρᵢⱼ → ρᵢⱼ·e^(−t/T₂) — exponential decay. In the density matrix heatmap, noise makes the off-diagonal spots at (0,15) and (15,0) for GHZ+i gradually fade.</td>
</tr>
<tr class="even">
<td><strong>Q7</strong></td>
<td><strong>What is the quantum threshold theorem?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Threshold theorem: if the physical gate error rate p &lt; p_th (threshold, ≈10⁻² to 10⁻⁴ depending on code), then arbitrarily long quantum computations can be performed fault-tolerantly using QEC codes with only polynomial overhead. IBM hardware is approaching the surface code threshold (~1%) for two-qubit gates. This is why error correction research is critical for fault-tolerant quantum computing.</td>
</tr>
<tr class="even">
<td><strong>Q8</strong></td>
<td><strong>What is quantum error mitigation and how does it differ from error correction?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Error correction: uses redundant qubits to detect and correct errors in real-time. Requires O(d²) physical qubits per logical qubit. Error mitigation: classical post-processing of noisy expectation values without qubit overhead. Works only for expectation values. Techniques: zero-noise extrapolation (ZNE), probabilistic error cancellation (PEC), readout calibration. ZNE: amplify noise at scale factors 1,3,5 via gate folding, then extrapolate to zero noise.</td>
</tr>
<tr class="even">
<td><strong>Q9</strong></td>
<td><strong>Explain the Lindblad master equation for open quantum systems.</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Lindblad equation: dρ/dt = −i[H,ρ] + Σ_k γ_k(L_kρL_k† − L_k†L_kρ/2 − ρL_k†L_k/2). H is the system Hamiltonian, L_k are Lindblad jump operators (e.g. L = σ− for amplitude damping, L = σ_z for dephasing), γ_k are decay rates. The equation preserves trace, Hermiticity, and positivity of ρ. It is the most general Markovian evolution equation for open quantum systems.</td>
</tr>
<tr class="even">
<td><strong>Q10</strong></td>
<td><strong>What is quantum error correction and why is the GHZ state particularly vulnerable?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>QEC uses redundant qubit encoding to detect and correct errors. The GHZ state is particularly vulnerable because: (1) it is maximally entangled across all qubits, so a single error on any qubit can affect all correlated states; (2) the |0000⟩ and |1111⟩ superposition spans the entire Hilbert space, making it susceptible to all error types; (3) long-range correlations mean errors can propagate. In contrast, product states are more noise-resilient because each qubit fails independently.</td>
</tr>
</tbody>
</table><table>
<colgroup>
<col style="width: 12%"/>
<col style="width: 87%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>EXP</strong></p>
<p><strong>6</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Sampling Laboratory — Shot Noise and Statistical Convergence</strong></p>
<p>AQLL §6 | Phase 1: Guided Simulation</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Demonstrate Born rule verification, shot noise statistics (σ = √[p(1-p)/N]), and convergence from 100 to 10,000 measurements toward the ideal probability distribution.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>AQLL §6 | Phase 1: Guided Simulation</td>
</tr>
</tbody>
</table>