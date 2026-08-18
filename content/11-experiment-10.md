<h1 id="experiment-10-grovers-search-algorithm-amplitude-amplification">Experiment 10: Grover's Search Algorithm — Amplitude Amplification</h1><table>
<colgroup>
<col style="width: 12%"/>
<col style="width: 87%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>EXP</strong></p>
<p><strong>10</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Grover's Search Algorithm — Amplitude Amplification</strong></p>
<p>AQLL §7d | Phase 1: Guided Simulation</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Implement Grover's search for a 4-qubit (N=16) database; observe amplitude oscillation over iterations; verify optimal iteration count k_opt = ⌊π√N/4⌋ = 3.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>AQLL §7d | Phase 1: Guided Simulation</td>
</tr>
</tbody>
</table><h2 id="background-theory-9">1. Background Theory</h2><p>Grover's Algorithm: O(√N) quantum oracle queries vs O(N) classical. For N=16 (4 qubits): <img loading="lazy" src="content/images/image54.png"/>. <img loading="lazy" src="content/images/image55.png"/> where θ = arcsin(1/4) ≈ 14.48°. Circuit: (1) uniform superposition H⁴, (2) Phase oracle: marks target with -1 phase, (3) Diffusion operator: 2|s⟩⟨s|−I. Over-iteration: P oscillates, decreasing after k_opt.</p><h2 id="qiskit-code-8">2. Qiskit Code</h2><h3 id="first-program-simple-version-9">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># Experiment 10 — First Program: Grover's Search Algorithm — Amplitude Amplification
# AQLL §7d | Dr. S. K. Jain, Invertis University, India
# Grover's Search Algorithm — Amplitude Amplification
# ---------------------------------------------------------------------
from qiskit import QuantumCircuit, transpile
from qiskit_aer import AerSimulator
import numpy as np
n = 4 # 4 qubits, N = 16 database entries
target = '1011' # marked item (bit i &lt;-&gt; qubit i, LSB first)
k_opt = int(np.floor(np.pi * np.sqrt(2**n) / 4)) # optimal iterations = 3
def oracle(qc, target):
for i, b in enumerate(reversed(target)):
if b == '0':
qc.x(i)
qc.h(n - 1); qc.mcx(list(range(n - 1)), n - 1); qc.h(n - 1)
for i, b in enumerate(reversed(target)):
if b == '0':
qc.x(i)
def diffuser(qc):
qc.h(range(n)); qc.x(range(n))
qc.h(n - 1); qc.mcx(list(range(n - 1)), n - 1); qc.h(n - 1)
qc.x(range(n)); qc.h(range(n))
qc = QuantumCircuit(n, n)
qc.h(range(n)) # uniform superposition
for _ in range(k_opt):
oracle(qc, target)
diffuser(qc)
qc.measure(range(n), range(n))
sim = AerSimulator()
counts = sim.run(transpile(qc, sim), shots=4096).result().get_counts()
p_target = counts.get(target, 0) / 4096
theta = np.arcsin(1/np.sqrt(2**n))
print(f'Optimal iterations k_opt = {k_opt}')
print(f'Target state |{target}&gt;: measured probability = {p_target:.4f}')
print(f'Theoretical P(target) = sin^2((2k+1)*theta) = {np.sin((2*k_opt+1)*theta)**2:.4f}')
print('Top 5 measured outcomes:')
for state, c in sorted(counts.items(), key=lambda x: -x[1])[:5]:
print(f' |{state}&gt;: {c} counts ({c/4096:.4f})')</code></pre><p><strong>Expected Output:</strong></p><div class="box box-generic"><p>▶ Exp 10 — Expected Output &amp; Console Results</p><p>Optimal iterations k_opt = 3</p><p>Target state |1011&gt;: measured probability = 0.9626</p><p>Theoretical P(target) = sin^2((2k+1)*theta) = 0.9613</p><p>Top 5 measured outcomes:</p><p>|1011&gt;: 3943 counts (0.9626)</p><p>|0000&gt;: 22 counts (0.0054)</p><p>|1111&gt;: 13 counts (0.0032)</p><p>|0010&gt;: 12 counts (0.0029)</p><p>|1010&gt;: 12 counts (0.0029)</p></div><h3 id="full-program-complete-version-8">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ───────────────────────────────────────────────────────────────────────
# Experiment 10: Grover's Search Algorithm — Amplitude Amplification
# # AQLL §7d | Dr. S. K. Jain, Invertis University, India
# ───────────────────────────────────────────────────────────────────────
from qiskit import QuantumCircuit, transpile
from qiskit_aer import AerSimulator
import numpy as np, matplotlib.pyplot as plt
n = 4
N = 2**n
target = '1011'
sim = AerSimulator()
def oracle(qc, t):
for i, b in enumerate(reversed(t)):
if b == '0':
qc.x(i)
qc.h(n - 1); qc.mcx(list(range(n - 1)), n - 1); qc.h(n - 1)
for i, b in enumerate(reversed(t)):
if b == '0':
qc.x(i)
def diffuser(qc):
qc.h(range(n)); qc.x(range(n))
qc.h(n - 1); qc.mcx(list(range(n - 1)), n - 1); qc.h(n - 1)
qc.x(range(n)); qc.h(range(n))
def run_grover(k, t=target):
qc = QuantumCircuit(n, n)
qc.h(range(n))
for _ in range(k):
oracle(qc, t)
diffuser(qc)
qc.measure(range(n), range(n))
counts = sim.run(transpile(qc, sim), shots=4096).result().get_counts()
return counts.get(t, 0) / 4096, qc
# ---- Part 1: amplitude oscillation over iterations 0..8 -------------------
iterations = range(0, 9)
measured, theory = [], []
theta = np.arcsin(1/np.sqrt(N))
for k in iterations:
p, _ = run_grover(k)
measured.append(p)
theory.append(np.sin((2*k + 1) * theta) ** 2)
print(f'k={k}: measured P(target)={p:.4f} theoretical={theory[-1]:.4f}')
k_opt = int(np.floor(np.pi * np.sqrt(N) / 4))
print(f'\nOptimal k_opt = floor(pi*sqrt(N)/4) = {k_opt}')
# ---- Part 2: classical vs quantum query comparison -------------------------
classical_avg_queries = N / 2
quantum_queries = k_opt
print(f'Classical average queries needed: {classical_avg_queries:.1f}')
print(f'Grover queries needed: {quantum_queries} '
f'(speed-up factor ~ {classical_avg_queries/quantum_queries:.2f}x)')
# ---- Part 3: search for several different targets at k_opt ----------------
other_targets = ['0000', '0110', '1111', '1011']
target_probs = {}
for t in other_targets:
p, _ = run_grover(k_opt, t)
target_probs[t] = p
print(f'Target |{t}&gt;: P = {p:.4f}')
# ---- Part 4: circuit resource analysis -------------------------------------
_, qc_opt = run_grover(k_opt)
tqc = transpile(qc_opt, sim, optimization_level=1)
print(f'\nCircuit depth at k_opt: {tqc.depth()}, gate count: {tqc.size()}')
# ---- Multi-panel plot -------------------------------------------------------
fig, axes = plt.subplots(1, 3, figsize=(15, 4.5))
axes[0].plot(iterations, measured, 'o-', label='Measured (simulation)')
axes[0].plot(iterations, theory, 's--', label='Theory sin^2((2k+1)theta)')
axes[0].axvline(k_opt, color='red', linestyle=':', label=f'k_opt={k_opt}')
axes[0].set_xlabel('Grover iterations k'); axes[0].set_ylabel('P(target)')
axes[0].set_title('Amplitude Amplification vs Iteration Count')
axes[0].legend(fontsize=8)
axes[1].bar(target_probs.keys(), target_probs.values(), color='#4C72B0')
axes[1].axhline(1/N, color='gray', linestyle='--', label='Random guess = 1/N')
axes[1].set_title(f'Success Probability at k_opt={k_opt} for Different Targets')
axes[1].set_ylabel('P(correct target)'); axes[1].legend(fontsize=8)
axes[2].bar(['Classical\n(avg)', 'Grover\n(k_opt)'],
[classical_avg_queries, quantum_queries], color=['#C44E52', '#55A868'])
axes[2].set_title('Classical vs Quantum Query Complexity')
axes[2].set_ylabel('Number of oracle queries')
plt.tight_layout()
plt.savefig('lab10_grover_full_analysis.png', dpi=150)
plt.show()</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image56.png"/></figure><div class="box box-generic"><p>▶ Exp 10 — Expected Output &amp; Console Results</p><p>k=0: measured P(target)=0.0684 theoretical=0.0625</p><p>k=1: measured P(target)=0.4727 theoretical=0.4727</p><p>k=2: measured P(target)=0.9146 theoretical=0.9084</p><p>k=3: measured P(target)=0.9641 theoretical=0.9613</p><p>k=4: measured P(target)=0.5864 theoretical=0.5817</p><p>k=5: measured P(target)=0.1196 theoretical=0.1255</p><p>k=6: measured P(target)=0.0210 theoretical=0.0204</p><p>k=7: measured P(target)=0.3633 theoretical=0.3649</p><p>k=8: measured P(target)=0.8274 theoretical=0.8361</p><p>Optimal k_opt = floor(pi*sqrt(N)/4) = 3</p><p>Classical average queries needed: 8.0</p><p>Grover queries needed: 3 (speed-up factor ~ 2.67x)</p><p>Circuit depth at k_opt: 14, gate count: 37</p></div><h2 id="observation-and-results-9">3. Observation and Results</h2><h3 id="observation-tables-2">Observation Tables</h3><p><em>Complete the observation tables for Experiment 10. Fill in during the practical session using actual experimental data from AQLL and your own program.</em></p><table>
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
</table><h2 id="discussion-questions-9">4. Discussion Questions</h2><ul>
<li><p>Why does over-iterating past k_opt actually decrease the probability of measuring the target state, instead of continuing to increase it?</p></li>
<li><p>How does the number of required Grover iterations scale with the database size N, and why does this represent only a quadratic (not exponential) speed-up over classical search?</p></li>
<li><p>If there were multiple marked items instead of a single target, how would you expect k_opt to change?</p></li>
<li><p>Write complete answers in your lab record with supporting calculations and diagrams.</p></li>
</ul><h2 id="lab-record-requirements-9">5. Lab Record Requirements</h2><ul>
<li><p>Run AQLL module for Experiment 10. Screenshot all output panels.</p></li>
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
<td><strong>What are the key quantum concepts demonstrated in Experiment 10?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Experiment 10 demonstrates: Grover's Algorithm: O(√N) quantum oracle queries vs O(N) classical. For N=16 (4 qubits): k_opt = ⌊π√16/4⌋ = ⌊π⌋ = 3. P(target, k=3) = sin²(7θ) ≈ 0.961 where θ = arcsin(1/4) ≈ 14.48°. Circuit: (1) uniform superposition H⁴, (2) Phase oracle: marks target with -1 phase, (3) Diffusion operator: 2|s⟩⟨s|−... Students should understand both the theoretical foundations and the practical Qiskit implementation, and be able to explain the significance of each result in terms of quantum information science principles covered in this manual.</td>
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
<td><strong>Why is the Grover diffusion operator described as an “inversion about the average” amplitude?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The diffusion operator 2|s⟩⟨s|−I reflects every amplitude about the mean amplitude of the current superposition. Since the oracle has already flipped the sign of the marked state's amplitude, this reflection increases the marked amplitude while decreasing the others, amplifying the probability of measuring the target.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>What happens to the required number of Grover iterations if the number of marked items M increases from 1 to some larger value?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The optimal iteration count becomes k_opt ≈ (π/4)√(N/M), which decreases as M grows, since fewer iterations are needed to amplify the combined probability of a larger set of marked states. If M becomes comparable to N, very few (or even zero) iterations may be optimal.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>Why is Grover's algorithm considered optimal, and in what sense can it not be improved further?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>It has been proven that any quantum algorithm solving unstructured search must make at least Ω(√N) oracle queries, matching Grover's O(√N) query complexity up to constant factors. This means no quantum algorithm can search an unsorted database asymptotically faster than Grover's, making it query-optimal.</td>
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
<p><strong>11</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Expectation Value Sweeps and Quantum Correlations</strong></p>
<p>AQLL §8 | Phase 1: Guided Simulation</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Compute ⟨ZZ⟩, ⟨XX⟩, ⟨YY⟩ two-qubit correlation functions for GHZ+i state and sweep a gate parameter to show how correlations evolve.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>AQLL §8 | Phase 1: Guided Simulation</td>
</tr>
</tbody>
</table>