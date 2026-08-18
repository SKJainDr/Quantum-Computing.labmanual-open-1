<div class="box box-key-concept"><p class="box-title"><strong>PHASE 1: GUIDED SIMULATION</strong></p><p>Experiments 1–11 | AQLL v3.0 Modules</p></div><h1 id="experiment-1-ghzi-state-evolution-gate-by-gate-visualisation">Experiment 1: GHZ+i State Evolution — Gate-by-Gate Visualisation</h1><table>
<colgroup>
<col style="width: 12%"/>
<col style="width: 87%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>EXP</strong></p>
<p><strong>1</strong></p>
<p>3 hrs</p></td>
<td><p><strong>GHZ+i State Evolution — Gate-by-Gate Visualisation</strong></p>
<p>AQLL §1 | Phase 1: Guided Simulation</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Construct the 4-qubit GHZ+i entangled state step-by-step, visualise each gate's effect on Bloch spheres and amplitudes, and interpret the resulting maximal entanglement.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>AQLL §1 | Phase 1: Guided Simulation</td>
</tr>
</tbody>
</table><h2 id="background-theory">1. Background Theory</h2><p>The Greenberger-Horne-Zeilinger (GHZ) state is the prototypical maximally entangled multi-qubit state in quantum information science. For n qubits, the GHZ state is:</p><figure class="book-figure"><img loading="lazy" src="content/images/image3.png"/></figure><p>The GHZ+i variant introduces a relative imaginary phase factor i = e^(iπ/2) between the two computational basis components:</p><div class="box box-generic"><p>|ψ⟩ = (|0000⟩ + i|1111⟩) / √2 — GHZ+i Target State</p></div><p>This phase is physically significant: it is invisible to Z-basis measurements (since |amplitude|² = 0.5 for both |0000⟩ and |1111⟩ regardless of the phase), but it manifests clearly in the off-diagonal elements of the density matrix and in expectation values of operators containing X or Y components. The off-diagonal coherence ρ₀₁₅ is purely imaginary (+0.5i), confirming the π/2 relative phase.</p><p>The 6-gate construction sequence is:</p><table>
<colgroup>
<col style="width: 6%"/>
<col style="width: 10%"/>
<col style="width: 12%"/>
<col style="width: 70%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Step</strong></td>
<td><strong>Gate</strong></td>
<td><strong>Acts On</strong></td>
<td><strong>Physical Effect</strong></td>
</tr>
<tr class="even">
<td>0</td>
<td>—</td>
<td>—</td>
<td>Initial state |0000⟩: all qubits at the north pole of the Bloch sphere</td>
</tr>
<tr class="odd">
<td>1</td>
<td>H</td>
<td>Q0</td>
<td>|0⟩ → (|0⟩+|1⟩)/√2: Q0 moves to equator. Superposition created.</td>
</tr>
<tr class="even">
<td>2</td>
<td>P(π/2)</td>
<td>Q0</td>
<td>Multiplies |1⟩ component by e^(iπ/2)=i. 90° azimuthal rotation on Bloch equator.</td>
</tr>
<tr class="odd">
<td>3</td>
<td>CNOT</td>
<td>Q0→Q1</td>
<td>Entangles Q1: (|00⟩+i|11⟩)/√2. Q2, Q3 still unentangled.</td>
</tr>
<tr class="even">
<td>4</td>
<td>CNOT</td>
<td>Q0→Q2</td>
<td>Extends entanglement to Q2. Three-qubit correlation established.</td>
</tr>
<tr class="odd">
<td>5</td>
<td>CNOT</td>
<td>Q0→Q3</td>
<td>Completes chain: (|0000⟩+i|1111⟩)/√2. All four qubits maximally entangled.</td>
</tr>
</tbody>
</table><h2 id="understanding-the-three-panel-aqll-visualisation">2. Understanding the Three-Panel AQLL Visualisation</h2><h3 id="panel-1-bloch-sphere">Panel 1 — Bloch Sphere</h3><p>The Bloch sphere represents the state of a single qubit as a point on a unit sphere. The north pole (0,0,1) corresponds to |0⟩, the south pole (0,0,−1) to |1⟩, and equatorial points to superpositions. The Bloch vector length r satisfies:</p><div class="box box-generic"><p>r = 1 for pure single-qubit state | r &lt; 1 for mixed/entangled state | r = 0 for maximally entangled</p></div><p>After the Hadamard gate (Step 1), Q0's Bloch vector lies on the equator (r=1, θ=π/2). After the first CNOT (Step 3), both Q0 and Q1 become entangled — their individual Bloch vectors shrink toward the center (r &lt; 1). After Step 5, all four vectors collapse to the origin (r = 0), confirming maximal 4-qubit entanglement.</p><h3 id="panel-2-state-city-amplitude-bars">Panel 2 — State City (Amplitude Bars)</h3><p>The State City shows real and imaginary parts of every 16 basis-state amplitudes as 3D bars. Initially only one bar (|0000⟩, real part 1). After the phase gate, the imaginary amplitude of |0001⟩ (Qiskit little-endian notation) becomes non-zero. After all CNOTs: exactly two bars remain — one real bar for |0000⟩ and one imaginary bar for |1111⟩. No other basis states appear, confirming GHZ+i structure.</p><h3 id="panel-3-probability-histogram">Panel 3 — Probability Histogram</h3><p>Shows measurement probabilities |amplitude|² for each basis state. For GHZ+i: exactly two bars at 0.5 each for |0000⟩ and |1111⟩. The phase factor i is completely invisible here — it does not affect measurement statistics. This demonstrates that phase information, though physically real and detectable through interference, is inaccessible via simple projective measurement in the computational basis.</p><h2 id="expected-aqll-output">3. Expected AQLL Output</h2><div class="box box-generic"><p>Expected Amplitude Table (Final State):</p><p>|0000⟩ : Re = +0.7071 Im = 0.0000 Probability = 0.5000</p><p>|1111⟩ : Re = 0.0000 Im = +0.7071 Probability = 0.5000</p><p>All other states: Re = 0, Im = 0, Probability = 0</p><p>Note: 0.7071 = 1/√2. Any deviation indicates a circuit error.</p></div><h2 id="qiskit-code-ghzi-state-construction">4. Qiskit Code — GHZ+i State Construction</h2><h3 id="first-program-simple-version">First Program (Simple Version)</h3><p>This concise program builds the GHZ+i state and prints the non-zero amplitude table. Run this first to verify your circuit is correct.</p><pre><code class="language-python"># ------------------------------------------------------------
# Experiment 1 — First Program: GHZ+i State (simple version)
# Dr. S. K. Jain, India
# ------------------------------------------------------------
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector
import numpy as np
qc = QuantumCircuit(4)
qc.h(0) # Step 1: Hadamard on Q0
qc.p(np.pi/2, 0) # Step 2: Phase(π/2) → introduces i factor
qc.cx(0, 1) # Step 3: CNOT Q0→Q1
qc.cx(0, 2) # Step 4: CNOT Q0→Q2
qc.cx(0, 3) # Step 5: CNOT Q0→Q3
sv = Statevector(qc)
print('State amplitudes (non-zero only):')
for i, amp in enumerate(sv.data):
if abs(amp) &gt; 1e-10:
print(f' |{i:04b}&gt; : Re={amp.real:+.4f} Im={amp.imag:+.4f} P={abs(amp)**2:.4f}')
print(qc.draw('text'))</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image4.png"/></figure><pre><code>▶ Exp 1 First Program — Circuit Diagram &amp; Expected Console Output
CONSOLE OUTPUT (amplitude table):
┌──────────┬───────────┬───────────┬─────────┐
│ State │ Re(amp) │ Im(amp) │ Prob │
├──────────┼───────────┼───────────┼─────────┤
│ |0000&gt; │ +0.7071 │ 0.0000 │ 0.5000 │ ← equal superposition
│ |1111&gt; │ 0.0000 │ +0.7071 │ 0.5000 │ ← imaginary amplitude!
└──────────┴───────────┴───────────┴─────────┘
All other 14 basis states have amplitude = 0</code></pre><h3 id="full-program-complete-version-with-step-by-step-analysis">Full Program (Complete Version with Step-by-Step Analysis)</h3><p>This comprehensive program performs a full gate-by-gate analysis with Bloch vector tracking, step-by-step probability histograms, and multi-panel visualisations. Run after the First Program.</p><pre><code class="language-python"># ───────────────────────────────────────────────────────────────────────
# Experiment 1: GHZ+i State Evolution — Quantum Computing Lab I (Full version)
# AQLL §1 | Dr. S. K. Jain, India
# ───────────────────────────────────────────────────────────────────────
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector, partial_trace
from qiskit.visualization import plot_bloch_multivector, plot_state_city
import numpy as np
import matplotlib.pyplot as plt
def build_ghz_plus_i():
"""Constructs the 4-qubit GHZ+i state: (|0000⟩ + i|1111⟩) / √2"""
qc = QuantumCircuit(4)
qc.h(0) # Step 1: Hadamard — creates superposition on Q0
qc.p(np.pi/2, 0) # Step 2: Phase gate P(π/2) — multiplies |1⟩ by i
qc.cx(0, 1) # Step 3: CNOT Q0→Q1 — entangle Q1
qc.cx(0, 2) # Step 4: CNOT Q0→Q2 — extend entanglement
qc.cx(0, 3) # Step 5: CNOT Q0→Q3 — complete GHZ chain
return qc
# Build circuit and compute statevector
qc = build_ghz_plus_i()
sv = Statevector(qc)
# ── Print complete amplitude table ──────────────────────────────────────────
print('GHZ+i Amplitude Table:')
print(f'{"State":&gt;8} {"Re":&gt;8} {"Im":&gt;8} {"Prob":&gt;8}')
for i, amp in enumerate(sv.data):
if abs(amp) &gt; 1e-10:
state = format(i, '04b')
print(f'|{state}&gt; {amp.real:&gt;8.4f} {amp.imag:&gt;8.4f} {abs(amp)**2:&gt;8.4f}')
# ── Step-by-step gate analysis ──────────────────────────────────────────
step_gates = [
('Initial |0000⟩', []),
('After H(Q0)', [('h', 0)]),
('After P(π/2,Q0)', [('h', 0), ('p', np.pi/2, 0)]),
('After CNOT Q0→Q1',[('h', 0), ('p', np.pi/2, 0), ('cx', 0, 1)]),
('After CNOT Q0→Q2',[('h', 0), ('p', np.pi/2, 0), ('cx', 0, 1), ('cx', 0, 2)]),
('After CNOT Q0→Q3',[('h', 0), ('p', np.pi/2, 0), ('cx', 0, 1), ('cx', 0, 2), ('cx', 0, 3)]),
]
fig, axes = plt.subplots(2, 6, figsize=(24, 8))
for idx, (label, gates) in enumerate(step_gates):
qc_step = QuantumCircuit(4)
for g in gates:
if g[0] == 'h': qc_step.h(g[1])
elif g[0] == 'p': qc_step.p(g[1], g[2])
elif g[0] == 'cx': qc_step.cx(g[1], g[2])
sv_step = Statevector(qc_step)
# Bloch vector lengths for each qubit (r=0 means maximally entangled)
bloch_vecs = []
for qi in range(4):
rho_i = partial_trace(sv_step, [q for q in range(4) if q != qi])
bx = float(np.real(rho_i.data[0,1] + rho_i.data[1,0]))
by = float(np.real(-1j*(rho_i.data[0,1] - rho_i.data[1,0])))
bz = float(np.real(rho_i.data[0,0] - rho_i.data[1,1]))
bloch_vecs.append(np.sqrt(bx**2 + by**2 + bz**2))
# Row 0: Bloch vector lengths per qubit per step
axes[0, idx].bar([f'Q{i}' for i in range(4)], bloch_vecs,
color=['#1A3C6E','#2E75B6','#0D5C63','#C09010'])
axes[0, idx].set_ylim(0, 1.1)
axes[0, idx].set_title(label, fontsize=8, fontweight='bold')
axes[0, idx].set_ylabel('|Bloch Vector|')
# Row 1: Probability histogram per step
probs = np.abs(sv_step.data)**2
axes[1, idx].bar(range(16), probs, color='#2E75B6', edgecolor='white')
axes[1, idx].set_xlabel('Basis state (index)')
axes[1, idx].set_ylim(0, 1.1)
plt.suptitle('GHZ+i State — Gate-by-Gate Analysis', fontsize=14, fontweight='bold')
plt.tight_layout()
plt.savefig('lab1_ghz_stepbystep.png', dpi=150, bbox_inches='tight')
plt.show()
# ── Bloch sphere visualisation (final state) ──────────────────────
fig2 = plot_bloch_multivector(sv)
fig2.suptitle('GHZ+i Final State — All Bloch Vectors at Origin (Maximal Entanglement)',
fontsize=11, fontweight='bold')
plt.tight_layout()
plt.savefig('lab1_ghz_bloch_final.png', dpi=150, bbox_inches='tight')
plt.show()
# ── Print circuit diagram ──────────────────────────────────────────
print('\nCircuit Diagram:')
print(qc.draw('text'))
qc.draw('mpl', filename='lab1_circuit.png', style={'backgroundcolor': '#FFFFFF'})
plt.show()</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image5.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image6.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image7.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image8.png"/></figure><h2 id="observation-and-results">5. Observation and Results</h2><h3 id="table-1.1-gate-by-gate-state-evolution">Table 1.1 — Gate-by-Gate State Evolution</h3><table>
<colgroup>
<col style="width: 22%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Gate Step</strong></td>
<td><strong>Bloch Length Q0</strong></td>
<td><strong>Bloch Length Q1-Q3</strong></td>
<td><strong>Non-zero Amplitudes</strong></td>
<td><strong>Purity</strong></td>
</tr>
<tr class="even">
<td>Step 0 — |0000⟩</td>
<td>1.0 (north pole)</td>
<td>1.0 (north pole)</td>
<td>1 (|0000⟩)</td>
<td>1.0</td>
</tr>
<tr class="odd">
<td>Step 1 — H(Q0)</td>
<td>1.0 (equator)</td>
<td>1.0 (unchanged)</td>
<td>2 (|0000⟩, |0001⟩)</td>
<td>1.0</td>
</tr>
<tr class="even">
<td>Step 2 — P(π/2,Q0)</td>
<td>1.0 (equator, 90° azimuth)</td>
<td>1.0 (unchanged)</td>
<td>2 (|0000⟩, |0001⟩ with i)</td>
<td>1.0</td>
</tr>
<tr class="odd">
<td>Step 3 — CNOT(Q0→Q1)</td>
<td>&lt;1 (entangled)</td>
<td>Q1: &lt;1; Q2,Q3: 1.0</td>
<td>2 (|0000⟩, |0011⟩)</td>
<td>1.0</td>
</tr>
<tr class="even">
<td>Step 4 — CNOT(Q0→Q2)</td>
<td>&lt;1 (more entangled)</td>
<td>Q1,Q2: &lt;1; Q3: 1.0</td>
<td>2 (|0000⟩, |0111⟩)</td>
<td>1.0</td>
</tr>
<tr class="odd">
<td>Step 5 — CNOT(Q0→Q3)</td>
<td>~0 (maximal)</td>
<td>~0 (all maximal)</td>
<td>2 (|0000⟩, |1111⟩)</td>
<td>1.0</td>
</tr>
</tbody>
</table><h3 id="table-1.2-amplitude-table-record-from-aqll-output-program">Table 1.2 — Amplitude Table (Record from AQLL Output / Program)</h3><table>
<colgroup>
<col style="width: 19%"/>
<col style="width: 16%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
<col style="width: 25%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Basis State</strong></td>
<td><strong>Binary Index</strong></td>
<td><strong>Re(amplitude)</strong></td>
<td><strong>Im(amplitude)</strong></td>
<td><strong>Probability |amp|²</strong></td>
</tr>
<tr class="even">
<td>|0000⟩</td>
<td>0 (index)</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>|0001⟩</td>
<td>1</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>|0010⟩</td>
<td>2</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>|0011⟩ … |1110⟩</td>
<td>3–14</td>
<td>0</td>
<td>0</td>
<td>0 (all zero)</td>
</tr>
<tr class="even">
<td>|1111⟩</td>
<td>15</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><p><em>Note: Record all non-zero amplitudes. Expected: only |0000⟩ and |1111⟩ should be non-zero.</em></p><h3 id="table-1.3-bloch-vector-length-at-each-step-record-from-program">Table 1.3 — Bloch Vector Length at Each Step (Record from Program)</h3><table>
<colgroup>
<col style="width: 14%"/>
<col style="width: 16%"/>
<col style="width: 16%"/>
<col style="width: 16%"/>
<col style="width: 16%"/>
<col style="width: 20%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Step</strong></td>
<td><strong>Q0</strong></td>
<td><strong>Q1</strong></td>
<td><strong>Q2</strong></td>
<td><strong>Q3</strong></td>
<td><strong>Sum of Lengths</strong></td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><h3 id="table-1.4-phase-variation-study">Table 1.4 — Phase Variation Study</h3><table>
<colgroup>
<col style="width: 21%"/>
<col style="width: 21%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
<col style="width: 18%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Phase Angle θ</strong></td>
<td><strong>State Obtained</strong></td>
<td><strong>Re(amp of |1111⟩)</strong></td>
<td><strong>Im(amp of |1111⟩)</strong></td>
<td><strong>AQLL Detectable?</strong></td>
</tr>
<tr class="even">
<td>θ = 0</td>
<td>GHZ standard</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>θ = π/4</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>θ = π/2 (GHZ+i)</td>
<td>GHZ+i</td>
<td>0</td>
<td>+0.7071</td>
<td>Only in ρ heatmap</td>
</tr>
<tr class="odd">
<td>θ = π</td>
<td>GHZ–</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>θ = 3π/2</td>
<td>GHZ−i</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><h2 id="discussion-questions">6. Discussion Questions</h2><ol type="1">
<li><p>At which gate step do the Bloch vectors first shrink below length 1? What physical phenomenon does this indicate?</p></li>
<li><p>Explain why the measurement histogram looks identical for |ψ⟩ = (|0000⟩ + |1111⟩)/√2 (standard GHZ) and (|0000⟩ + i|1111⟩)/√2 (GHZ+i). What additional measurement would distinguish them?</p></li>
<li><p>Predict the density matrix element ρ₀₁₅ for each phase angle in Table 1.4. Verify experimentally.</p></li>
<li><p>Why does entanglement increase after each CNOT gate but not after the H or P gates?</p></li>
<li><p>What is the Schmidt rank of |GHZ+i⟩ across the bipartition Q0Q1|Q2Q3? What does this tell you about its entanglement?</p></li>
</ol><h2 id="lab-record-requirements">7. Lab Record Requirements</h2><ul>
<li><p>Run AQLL v3.0 and observe all six step-by-step visualisations for §1. Screenshot each panel.</p></li>
<li><p>Print the amplitude table from AQLL output and highlight the two non-zero entries.</p></li>
<li><p>Run the First Program. Copy the console output into your lab record.</p></li>
<li><p>Run the Full Program. Save and paste the lab1_ghz_stepbystep.png and lab1_ghz_bloch_final.png into your lab record.</p></li>
<li><p>Complete Tables 1.1 through 1.4 using data from AQLL and both program runs.</p></li>
<li><p>Modify the phase angle from π/2 to π. Run and record results in Table 1.4.</p></li>
<li><p>Write answers to all five Discussion Questions above.</p></li>
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
<td><strong>What is a GHZ state and why is it important in quantum information?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>A GHZ (Greenberger-Horne-Zeilinger) state is a maximally entangled multi-qubit state of the form (|0...0⟩+|1...1⟩)/√2. It is important because it violates Bell-type inequalities more strongly than two-qubit Bell states, is a resource for quantum communication, secret sharing, and distributed quantum computing, and serves as a standard benchmark for entanglement generation on quantum hardware.</td>
</tr>
<tr class="even">
<td><strong>Q2</strong></td>
<td><strong>What is the role of the Hadamard gate in constructing the GHZ state?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The Hadamard gate creates a superposition on Q0: H|0⟩ = (|0⟩+|1⟩)/√2. This puts Q0 in an equal mixture of 0 and 1, which is then 'spread' to all other qubits via CNOT gates, resulting in the correlated superposition (|0000⟩+|1111⟩)/√2 — the GHZ state.</td>
</tr>
<tr class="even">
<td><strong>Q3</strong></td>
<td><strong>Why is the phase gate P(π/2) included, and what does it change?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>P(π/2)|0⟩=|0⟩, P(π/2)|1⟩=e^(iπ/2)|1⟩=i|1⟩. It introduces the imaginary phase factor i between the two superposition components. This converts the standard GHZ state to GHZ+i. The phase is invisible in Z-basis measurements but appears in Im(ρ) off-diagonal elements and in YY correlations.</td>
</tr>
<tr class="even">
<td><strong>Q4</strong></td>
<td><strong>How does a CNOT gate entangle two qubits?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>CNOT flips the target qubit if and only if the control qubit is |1⟩. When the control is in superposition α|0⟩+β|1⟩, the CNOT creates an entangled state α|00⟩+β|11⟩ — neither qubit has a definite state independently. This creates quantum correlations that cannot be described by any product state.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>What does the Bloch sphere vector length tell us about a qubit's state?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The Bloch vector length r equals 1 for a pure single-qubit state (no entanglement), and decreases toward 0 as entanglement with other qubits increases. For the GHZ+i state, each qubit's reduced state has r=0 (vector at origin) because each qubit is maximally entangled with the other three — a hallmark of maximal multi-qubit entanglement.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>Why do only two basis states (|0000⟩ and |1111⟩) appear in the final GHZ+i state?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The CNOT chain ensures that all qubits take the same value as Q0: if Q0=0, all are 0; if Q0=1, all are 1. After the Hadamard puts Q0 in superposition, the only possible outcomes are all-zeros or all-ones. All other 14 basis states have zero amplitude because no combination of gates in this circuit can produce partial-flip configurations.</td>
</tr>
<tr class="even">
<td><strong>Q7</strong></td>
<td><strong>What is the difference between |GHZ⟩ and |GHZ+i⟩? Can we distinguish them by measurement?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>|GHZ⟩ = (|0000⟩+|1111⟩)/√2 has all-real amplitudes; |GHZ+i⟩ = (|0000⟩+i|1111⟩)/√2 has an imaginary amplitude for |1111⟩. Simple Z-basis measurement cannot distinguish them — both give 50%/50% outcomes. Distinction requires interferometric measurements, e.g. measuring in the X or Y basis, or examining the off-diagonal Im(ρ) density matrix elements.</td>
</tr>
<tr class="even">
<td><strong>Q8</strong></td>
<td><strong>Define purity of a quantum state. What is the purity of the GHZ+i state?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Purity γ = Tr(ρ²) ∈ [1/d, 1]. γ=1 for a pure state, γ=1/d for the maximally mixed state of d-dimensional Hilbert space. The GHZ+i state is a pure state (it is a single ket vector |ψ⟩), so γ=Tr(|ψ⟩⟨ψ|)²=Tr(|ψ⟩⟨ψ|)=1 exactly. All unitary gates preserve purity, so γ=1 at every step of the circuit.</td>
</tr>
<tr class="even">
<td><strong>Q9</strong></td>
<td><strong>What is the no-cloning theorem and how does it relate to entanglement?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The no-cloning theorem states that it is impossible to create an independent and identical copy of an arbitrary unknown quantum state. It is a fundamental consequence of quantum linearity. Entanglement is not a violation: it creates correlations, not copies. The two qubits in a Bell state are not copies of each other — neither has a definite state individually.</td>
</tr>
<tr class="even">
<td><strong>Q10</strong></td>
<td><strong>How does the measurement probability formula (Born rule) work for the GHZ+i state?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The Born rule states P(outcome k) = |⟨k|ψ⟩|². For GHZ+i: P(|0000⟩) = |0.7071|² = 0.5; P(|1111⟩) = |i·0.7071|² = |0.7071|² = 0.5. The phase factor i does not affect the magnitude, so both outcomes have equal probability. This illustrates that phase information is hidden from projective measurements.</td>
</tr>
<tr class="even">
<td><strong>Q11</strong></td>
<td><strong>What would happen if you apply a second Hadamard gate to Q0 after the GHZ+i circuit?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Applying H to Q0 alone on an entangled state does not simply reverse the Hadamard. After CNOT entanglement, Q0 is entangled with Q1-Q3. The additional H would create a different entangled state, not disentangle the system. To fully decode the GHZ state, you would need to apply CNOT(Q0→Q1), CNOT(Q0→Q2), CNOT(Q0→Q3), then H(Q0) — i.e. the full inverse circuit.</td>
</tr>
<tr class="even">
<td><strong>Q12</strong></td>
<td><strong>Why are CNOT gates listed as two-qubit gates while H is a single-qubit gate?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>H acts on a single qubit's 2-dimensional state space (matrix is 2×2). CNOT acts on two qubits jointly — its matrix is 4×4 in the tensor product space. CNOT cannot be decomposed into independent single-qubit operations, making it an entangling gate. Single-qubit gates alone cannot create entanglement from a product state.</td>
</tr>
<tr class="even">
<td><strong>Q13</strong></td>
<td><strong>What is the Schmidt rank of the GHZ+i state across the Q0|Q1Q2Q3 bipartition?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The Schmidt decomposition across Q0|Q1Q2Q3 gives |GHZ+i⟩ = (1/√2)|0⟩_Q0|000⟩_Q1Q2Q3 + (i/√2)|1⟩_Q0|111⟩_Q1Q2Q3. The Schmidt rank = 2 (two terms). The Schmidt coefficients are both 1/√2. Since rank &gt; 1, the state is entangled across this bipartition. Maximum rank for this split = min(2,8)=2, so it is maximally entangled.</td>
</tr>
<tr class="even">
<td><strong>Q14</strong></td>
<td><strong>If you measure Q0 and find |0⟩, what state do Q1,Q2,Q3 collapse to?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>After measuring Q0=0, the state collapses to |0⟩_Q0 ⊗ |000⟩_Q1Q2Q3 (the coefficient of |0000⟩ is 1/√2, and renormalising gives |000⟩ with probability 1). If Q0=1 is measured, Q1Q2Q3 collapse to |111⟩. This is quantum steering — measuring one qubit instantly determines the others, regardless of spatial separation.</td>
</tr>
<tr class="even">
<td><strong>Q15</strong></td>
<td><strong>What is the physical significance of the imaginary unit i in quantum mechanics?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>In quantum mechanics, i = √−1 appears in Schrödinger's equation (iħ ∂ψ/∂t = Hψ) and in the structure of quantum amplitudes. A global phase e^(iφ) is physically irrelevant (unobservable), but a relative phase between two components of a superposition is physically real and measurable through interference. The i in GHZ+i is a relative phase that shows up in Im(ρ) and in YY correlations.</td>
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
<p><strong>2</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Density Matrix Analysis and Von Neumann Entropy</strong></p>
<p>AQLL §2 | Phase 1: Guided Simulation</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Construct and interpret the 16×16 density matrix for the GHZ+i state; compute purity and Von Neumann entropy; identify and explain the coherence heatmap structure.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>AQLL §2 | Phase 1: Guided Simulation</td>
</tr>
</tbody>
</table>