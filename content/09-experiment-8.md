<h1 id="experiment-8-chsh-bell-inequality-violation">Experiment 8: CHSH Bell Inequality Violation</h1><h2 id="background-theory-7">1. Background Theory</h2><p>In local realistic theories (classical physics with hidden variables), any correlation parameter S constructed from two separate measurement setups per observer satisfies CHSH Inequality:</p><p><img loading="lazy" src="content/images/image41.png"/> (classical local realism).</p><p>Quantum mechanics violates this local realistic limit. By preparing a maximally entangled Bell pair <img loading="lazy" src="content/images/image42.wmf"/> and carefully angling the detectors, quantum correlations push this value up to the <strong>Tsirelson Bound</strong> (quantum maximum): <img loading="lazy" src="content/images/image43.png"/>. Optimal measurement configurations rely on relative angular separation steps of 22.5 degrees (e.g., separating Alice's bases by 45 deg. and Bob's bases by 45 deg., offset from each other by 22.5 deg.) to achieve maximum constructive interference in the correlation parameters. Optimal measurement angles can be chosen as:</p><p>Alice’s settings (a, a’): a=0°, a’=45°; Bob’s settings (b, b’): b=22.5°, b’=67.5° → |S| = 2√2.</p><p>Or, equivalently as:</p><p>Alice’s settings (a, a’): a=0°, a’=90°; Bob’s settings (b, b’): b=45°, b’=135° → |S| = 2√2.</p><p>For a correlation measurement, what matters mathematically is the relative difference between the angles of Alice's detector and Bob's detector.</p><p>Nobel Prize 2022: Clauser, Aspect, Zeilinger — experimental violation of Bell inequalities.</p><h2 id="qiskit-code-6">2. Qiskit Code</h2><h3 id="first-program-simple-version-7">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># ─────────────────────────────────────────────────────────────────────
# Experiment 8: CHSH Bell Inequality Violation (Simple Version - Final)
# AQLL §7b | Dr. S. K. Jain, India
# ─────────────────────────────────────────────────────────────────────
import numpy as np
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister
from qiskit_aer import AerSimulator
def build_chsh_circuit(alice_angle, bob_angle):
"""
Constructs a 2-qubit CHSH circuit using explicit hardware registers.
Prepares a maximally entangled Bell state, rotates the analyzer bases,
and performs standard computational measurements.
"""
# Define explicit quantum and classical registers to lock in physical layout mapping
qr = QuantumRegister(2, 'q')
cr = ClassicalRegister(2, 'c')
qc = QuantumCircuit(qr, cr)
# STEP 1: State Preparation - Generate the Bell State |\Phi+&gt;
# Put Alice's qubit into a 50/50 superposition using a Hadamard gate
qc.h(qr[0])
# Entangle Bob's qubit with Alice's using a Controlled-NOT gate
qc.cx(qr[0], qr[1])
# Structural separation barrier before applying analyzer adjustments
qc.barrier()
# STEP 2: Detector Basis Rotations
# Qiskit measures in the Z-basis by default. To measure along an arbitrary angle,
# we apply a physical Y-axis rotation (RY) prior to measurement.
# We pass 2*theta to account for the Bloch sphere half-angle mapping rule.
qc.ry(2 * alice_angle, qr[0])
qc.ry(2 * bob_angle, qr[1])
qc.barrier()
# STEP 3: Measurement
# Map explicit quantum registers to corresponding classical bit slots
qc.measure(qr[0], cr[0])
qc.measure(qr[1], cr[1])
return qc
def calculate_expectation(counts, shots):
"""
Computes the correlation value E(a, b).
Formula: E = (Counts_00 + Counts_11 - Counts_01 - Counts_10) / Total_Shots
Symmetric outcomes yield +1 correlation; anti-symmetric outcomes yield -1.
"""
n_00 = counts.get('00', 0)
n_11 = counts.get('11', 0)
n_01 = counts.get('01', 0)
n_10 = counts.get('10', 0)
# Calculate the normalized expectation value
expectation = (n_00 + n_11 - n_01 - n_10) / shots
return expectation
# ──── MAIN SETUP &amp; EXECUTION ────
# Input Angles provided in degrees: a = 0°, a' = 45°, b = 22.5°, b' = 67.5°
# Convert degrees to Radians for mathematical compatibility with Qiskit
alice_angles = [np.radians(0), np.radians(45)]
bob_angles = [np.radians(22.5), np.radians(67.5)]
# High-count shot configurations to minimize statistical distribution noise
shots = 8192
# Instantiate the high-performance ideal simulation backend
simulator = AerSimulator()
# Dictionary container to hold expectation parameters
E_values = {}
print("--- Running CHSH Circuit Simulations ---")
# Loop through all 4 combinations: (A0,B0), (A0,B1), (A1,B0), (A1,B1)
for i, a_ang in enumerate(alice_angles):
for j, b_ang in enumerate(bob_angles):
# Generate the specific sub-circuit
qc = build_chsh_circuit(a_ang, b_ang)
# Execute the layout-locked circuit on the backend simulator
result = simulator.run(qc, shots=shots).result()
counts = result.get_counts()
# Process the statistical expectation output
E = calculate_expectation(counts, shots)
E_values[f"A{i}B{j}"] = E
print(f"Expectation E(A{i}, B{j}) = {E:+0.4f} | Counts: {counts}")
# Compute CHSH Test Parameter: S = E(A0,B0) - E(A0,B1) + E(A1,B0) + E(A1,B1)
# We apply absolute values to account for sign configurations under the specific angles chosen
S = abs(E_values["A0B0"] - E_values["A0B1"]) + abs(E_values["A1B0"] + E_values["A1B1"])
print("\n--- Final Results Summary ---")
print(f"Classical Local-Realism Bound: |S| &lt;= 2")
print(f"Quantum Theoretical Maximum (Tsirelson Bound): S = 2*sqrt(2) ≈ 2.8284")
print(f"Simulated Experimental Parameter |S|: {S:0.4f}")
# Evaluate violation criteria
if S &gt; 2:
print("SUCCESS: Bell Inequality Violated! Quantum Entanglement confirmed.")
else:
print("FAILURE: Bell Inequality satisfied. Check circuit setup parameters.")</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image44.png"/></figure><h3 id="full-program-complete-version-6">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ─────────────────────────────────────────────────────────────────────
# Experiment 8: CHSH Bell Inequality Violation (Full Complete Version)
# AQLL §7b | Dr. S. K. Jain, India
# ─────────────────────────────────────────────────────────────────────
import numpy as np
import matplotlib.pyplot as plt
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister
from qiskit_aer import AerSimulator
def run_chsh_sweep(theta_steps=25, shots=8192):
"""
Sweeps a relative angle parameter to generate a continuous curve
of the CHSH parameter S, showing the boundary transition.
"""
simulator = AerSimulator()
# Generate an array of base angles from 0 to 2*pi
sweep_angles = np.linspace(0, 2 * np.pi, theta_steps)
# Tracking structures for analysis logs and graphing
s_curve_data = []
text_table_logs = []
for theta in sweep_angles:
# Define the 4 configuration angles based on relative 22.5° offsets:
a0 = theta + np.radians(0)
a1 = theta + np.radians(45)
b0 = theta + np.radians(22.5)
b1 = theta + np.radians(67.5)
expectations = []
configurations = [(a0, b0), (a0, b1), (a1, b0), (a1, b1)]
# Execute the 4 unique combinations required per angle step
for alice_ang, bob_ang in configurations:
# Instantiate locked hardware structures to prevent layout remapping
qr = QuantumRegister(2, 'q')
cr = ClassicalRegister(2, 'c')
qc = QuantumCircuit(qr, cr)
# Step 1: Prepare Bell State
qc.h(qr[0])
qc.cx(qr[0], qr[1])
qc.barrier()
# Step 2: Orient measurement bases using the half-angle rule
qc.ry(2 * alice_ang, qr[0])
qc.ry(2 * bob_ang, qr[1])
qc.barrier()
# Step 3: Measurement
qc.measure(qr[0], cr[0])
qc.measure(qr[1], cr[1])
# Run simulation and extract statistics
counts = simulator.run(qc, shots=shots).result().get_counts()
# Calculate mathematical expectation value
n00 = counts.get('00', 0)
n11 = counts.get('11', 0)
n01 = counts.get('01', 0)
n10 = counts.get('10', 0)
E = (n00 + n11 - n01 - n10) / shots
expectations.append(E)
# Unpack expectations and combine terms
E_A0B0, E_A0B1, E_A1B0, E_A1B1 = expectations
S_value = abs(E_A0B0 - E_A0B1) + abs(E_A1B0 + E_A1B1)
# Log data for output generation
s_curve_data.append(S_value)
text_table_logs.append((theta, E_A0B0, E_A0B1, E_A1B0, E_A1B1, S_value))
return sweep_angles, s_curve_data, text_table_logs
# ──── RUN FULL SWEEP EXPERIMENT ────
steps = 25
total_shots = 8192
angles, s_data, table_rows = run_chsh_sweep(theta_steps=steps, shots=total_shots)
# ──── DISPLAY FORMATTED CONSOLE DATA ────
print(f"\n{'Sweep θ (rad)':&gt;14} {'E(A0,B0)':&gt;10} {'E(A0,B1)':&gt;10} {'E(A1,B0)':&gt;10} {'E(A1,B1)':&gt;10} {'|S|-Value':&gt;12}")
print("─" * 72)
for row in table_rows:
print(f"{row[0]:&gt;14.4f} {row[1]:&gt;10.3f} {row[2]:&gt;10.3f} {row[3]:&gt;10.3f} {row[4]:&gt;10.3f} {row[5]:&gt;12.4f}")
# ──── RENDER VERIFICATION LINE GRAPH ────
plt.figure(figsize=(10, 6))
# Highlight the classical local realism boundary corridor (|S| &lt;= 2)
plt.axhspan(0, 2, color='whitesmoke', alpha=1.0, label='Classical Local Realism Area (|S| eq-2)')
plt.axhline(2, color='red', linestyle='--', linewidth=1.2, label='Classical Boundary Threshold')
# Plot simulated data points
plt.scatter(angles, s_data, color='darkviolet', marker='o', s=40, zorder=5, label='Aer Simulator Sweeps')
plt.plot(angles, s_data, color='darkviolet', linestyle=':', alpha=0.5)
# Highlight standard maximum violation benchmarks (Tsirelson Bound)
plt.axhline(2 * np.sqrt(2), color='darkgreen', linestyle=':', linewidth=1)
plt.text(0.2, 2.9, 'Tsirelson Bound (2*sqrt{2} approx 2.828)', color='darkgreen', fontsize=9, fontweight='bold')
# Plot configurations setup
plt.title('Experiment 8: Full Angle Sweep - CHSH Bell Inequality Violation', fontsize=12, fontweight='bold')
plt.xlabel('Base Angle Reference theta (Radians)', fontsize=10)
plt.ylabel('Calculated CHSH Parameter (|S|)', fontsize=10)
plt.xlim(0, 2 * np.pi)
plt.ylim(0, 3.5)
plt.grid(True, which='both', linestyle=':', alpha=0.5)
plt.legend(loc='lower right')
# Export high-resolution chart asset to disk storage
plt.savefig('lab8_chsh_final_sweep.png', dpi=300)
plt.show()
print("\n[Execution Complete] Simulation completed. Graph saved as 'lab8_chsh_final_sweep.png'.")</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image45.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image46.png"/></figure><pre><code>▶ Exp 8 — Expected Output &amp; Console Results
CHSH MEASUREMENT SETUP:
┌─────────────────────────────────────────────────────┐
│ ALICE Bell Pair BOB │
│ measures a=0° |Φ+⟩=(|00⟩+|11⟩)/√2 b=22.5° │
│ or a'=45° q_0 ─[H]─■─── q_1 or b'=67.5° │
│ │ │
│ ←classical communication of angle choices→ │
└─────────────────────────────────────────────────────┘
Expected results for Experiment 8:
Theory: CHSH Inequality: |S| = |E(a,b) − E(a,b’) + E(a’,b) + E(a’,b’)|
Classical bound (Classical local realism): |S| ≤ 2
Tsirelson's bound (quantum realism): |S| ≤ 2√2 ≈ 2.8284
Our result: |S| = 2.8284 ✓ Bell violation!
Run the program above and paste your output here.
Compare with expected values listed in the observation tables below.</code></pre><h2 id="observation-and-results-7">3. Observation and Results</h2><h3 id="observation-tables">Observation Tables</h3><p><em>Complete the observation tables for Experiment 8. Fill in during the practical session using actual experimental data from AQLL and your own program.</em></p><h3 id="table-8.1-chsh-correlation-values">Table 8.1 — CHSH Correlation Values</h3><table>
<colgroup>
<col style="width: 29%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
<col style="width: 31%"/>
</colgroup>
<thead>
<tr class="header">
<th><strong>Measurement Angles</strong></th>
<th><strong>Correlation E(a,b)</strong></th>
<th><strong>AQLL Value</strong></th>
<th><strong>Formula Prediction</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>a=0°, b=22.5°</td>
<td>E(0°,22.5°)</td>
<td></td>
<td>cos(22.5°) ≈ 0.924</td>
</tr>
<tr class="even">
<td>a=0°, b'=67.5°</td>
<td>E(0°,67.5°)</td>
<td></td>
<td>cos(67.5°) ≈ 0.383</td>
</tr>
<tr class="odd">
<td>a'=45°, b=22.5°</td>
<td>E(45°,22.5°)</td>
<td></td>
<td>cos(22.5°) ≈ 0.924</td>
</tr>
<tr class="even">
<td>a'=45°, b'=67.5°</td>
<td>E(45°,67.5°)</td>
<td></td>
<td>−cos(22.5°) ≈ −0.924</td>
</tr>
<tr class="odd">
<td>|S| = |E_ab − E_ab' + E_a'b + E_a'b'|</td>
<td></td>
<td></td>
<td>2√2 ≈ 2.828</td>
</tr>
</tbody>
</table><h3 id="table-8.2-noise-threshold-analysis">Table 8.2 — Noise Threshold Analysis</h3><table>
<colgroup>
<col style="width: 23%"/>
<col style="width: 21%"/>
<col style="width: 25%"/>
<col style="width: 29%"/>
</colgroup>
<thead>
<tr class="header">
<th><strong>Noise Level p</strong></th>
<th><strong>CHSH |S| Value</strong></th>
<th><strong>Bell Violation (|S|&gt;2)?</strong></th>
<th><strong>F vs ideal Bell state</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>0.0</td>
<td></td>
<td>Yes</td>
<td>1.0</td>
</tr>
<tr class="even">
<td>0.1</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>0.2</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>0.3</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>p_critical (|S|=2)</td>
<td></td>
<td>No</td>
<td></td>
</tr>
</tbody>
</table><h3 id="table-8.3-program-output-vs-aqll-software-analysis">Table 8.3 — Program output vs AQLL software Analysis</h3><table>
<colgroup>
<col style="width: 27%"/>
<col style="width: 17%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
<col style="width: 16%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Parameter / Quantity</strong></td>
<td><strong>Expected Value</strong></td>
<td><strong>Measured Value (Simulation)</strong></td>
<td><strong>Measured Value (AQLL)</strong></td>
<td><strong>Notes</strong></td>
</tr>
<tr class="even">
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
</tr>
<tr class="even">
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
</tr>
<tr class="even">
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
</tr>
</tbody>
</table><h2 id="discussion-questions-7">4. Discussion Questions</h2><ul>
<li><p>Why must Alice's and Bob's measurement angle settings be different (not aligned) for the CHSH inequality to be violated? What happens to |S| if a = b?</p></li>
<li><p>The Tsirelson bound 2√2 is the quantum maximum for |S|. Why can quantum mechanics not violate the CHSH inequality by an arbitrarily large amount, the way a hypothetical “super-quantum” theory could?</p></li>
<li><p>If the |S| value measured on real IBM hardware comes out lower than 2√2, is this evidence against quantum mechanics, or does it point to something else? Explain what noise sources would account for the gap.</p></li>
<li><p>Write complete answers in your lab record with supporting calculations and diagrams.</p></li>
</ul><h2 id="lab-record-requirements-7">5. Lab Record Requirements</h2><ul>
<li><p>Run AQLL module for Experiment 8. Screenshot all output panels.</p></li>
<li><p>Run the First Program. Copy output to lab record.</p></li>
<li><p>Run the Full Program. Save all generated figures.</p></li>
<li><p>Complete all observation tables during the practical session.</p></li>
<li><p>Write complete answers to all Discussion Questions.</p></li>
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
<td><strong>What are the key quantum concepts demonstrated in Experiment 8?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Experiment 8 demonstrates: CHSH Inequality: |S| = |E(a,b) − E(a,b’) + E(a’,b) + E(a’,b’)| ≤ 2 (classical local realism). Tsirelson's Bound (quantum maximum): |S|_max = 2√2 ≈ 2.828. Optimal measurement angles: a=0°, a’=45°, b=22.5°, b’=67.5° → |S| = 2√2. Nobel Prize 2022: Clauser, Aspect, Zeilinger — experimental violation of ... Students should understand both the theoretical foundations and the practical Qiskit implementation, and be able to explain the significance of each result in terms of quantum information science principles covered in this manual.</td>
</tr>
<tr class="even">
<td><strong>Q2</strong></td>
<td><strong>How do you interpret the simulation vs hardware results for this experiment?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Ideal simulation (Qiskit-Aer, no noise model) gives theoretically perfect results. IBM hardware introduces gate errors (~0.01-1% per gate), readout errors (~1-5%), T₁ relaxation, T₂ dephasing, and crosstalk. The difference quantifies total experimental error. Always compare with calibration data from backend.properties() to attribute errors to specific sources.</td>
</tr>
<tr class="even">
<td><strong>Q3</strong></td>
<td><strong>What modifications would improve fidelity for this experiment on IBM hardware?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Improvements: (1) Select qubits with lowest CX error using backend.properties(). (2) Use optimization_level=3 to minimise circuit depth. (3) Apply readout error mitigation. (4) Apply ZNE with gate folding. (5) Increase shot count. (6) Run multiple repetitions and average.</td>
</tr>
<tr class="even">
<td><strong>Q4</strong></td>
<td><strong>Why is the CHSH parameter S constructed from four different correlation terms E(a,b), E(a,b'), E(a',b), and E(a',b') rather than just one?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>A single correlation term cannot distinguish quantum from classical behaviour, since any fixed pair of angles can be matched by a suitable local hidden-variable model. Combining four correlators measured at complementary angle settings creates a quantity whose classical bound (2) is provably lower than the quantum-achievable maximum (2√2), making the violation experimentally detectable.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>What role does the choice of measurement basis (rotation angle) play in maximising the CHSH violation?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The correlation E(a,b) depends on cos(2(a−b)) for a maximally entangled state, so choosing angles with a 22.5° relative separation between each pair maximises the constructive interference across all four terms simultaneously, producing the Tsirelson-bound value 2√2.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>Why can't a classical local hidden-variable theory reproduce |S| &gt; 2, even in principle?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>In any local hidden-variable model, each particle's measurement outcome is predetermined by variables local to it, independent of the other particle's distant setting. Bell (and later CHSH) proved algebraically that any such model is bounded by |S| ≤ 2, regardless of the specific hidden-variable distribution assumed.</td>
</tr>
<tr class="even">
<td><strong>Q7</strong></td>
<td><strong>How does the CHSH experiment rule out simple classical explanations like pre-agreed strategies between the two qubits?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>A pre-agreed (shared randomness) strategy is exactly the kind of local hidden-variable model the CHSH bound of 2 already accounts for and rules out. Since experiments consistently measure |S| &gt; 2, no classical pre-agreement, however cleverly designed, can reproduce the observed correlations.</td>
</tr>
<tr class="even">
<td><strong>Q8</strong></td>
<td><strong>What is the experimental and historical significance of the 2022 Nobel Prize awarded to Clauser, Aspect, and Zeilinger?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Their experiments provided increasingly rigorous, loophole-free demonstrations that nature violates Bell/CHSH inequalities, confirming that quantum mechanics cannot be explained by any local hidden-variable theory and establishing entanglement as a physically real, experimentally verified resource.</td>
</tr>
<tr class="even">
<td><strong>Q9</strong></td>
<td><strong>If you performed this experiment with a separable (non-entangled) two-qubit state instead of a Bell state, what value of |S| would you expect?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>For any separable (product) state, the correlations factorise and behave exactly like a classical local hidden-variable model, so |S| would be bounded by 2 and could not exceed the classical limit — no Bell violation would be observed.</td>
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
<p><strong>9</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Quantum Fourier Transform — Circuit and Analysis</strong></p>
<p>AQLL §7c | Phase 1: Guided Simulation</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Implement the 4-qubit QFT; verify uniform amplitude output on |0000⟩; observe the phase staircase structure; relate QFT to period finding in Shor's algorithm.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>AQLL §7c | Phase 1: Guided Simulation</td>
</tr>
</tbody>
</table>