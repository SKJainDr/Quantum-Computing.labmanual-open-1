<h1 id="experiment-11-expectation-value-sweeps-and-quantum-correlations">Experiment 11: Expectation Value Sweeps and Quantum Correlations</h1><h2 id="background-theory-10">1. Background Theory</h2><p>For GHZ+i = (|0000⟩+i|1111⟩)/√2, the two-qubit reduced correlators are ⟨ZZ⟩=+1.0 (perfect classical Z-correlation) but ⟨XX⟩=⟨YY⟩=0.0, since tracing out the other two qubits destroys the coherence between |0000⟩ and |1111⟩. That coherence instead shows up only in the full four-qubit correlators ⟨XXXX⟩=⟨YYYY⟩=cos θ and ⟨ZZZZ⟩=+1.0 for all θ (θ=π/2 for GHZ+i) — a hallmark of genuine multipartite, not merely pairwise, entanglement. Classical impossibility: no local hidden-variable model can reproduce these four-body correlators simultaneously. Angular sweep varies phase θ from 0 to 2π and tracks how ⟨ZZZZ⟩, ⟨XXXX⟩, ⟨YYYY⟩ evolve. The expectation value formula: ⟨O⟩ = Tr(ρO).</p><h2 id="qiskit-code-9">2. Qiskit Code</h2><h3 id="first-program-simple-version-10">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># Experiment 11 — First Program: Expectation Value Sweeps and Quantum Correlations
# AQLL §8 | Dr. S. K. Jain, Invertis University, India
# Expectation Value Sweeps and Quantum Correlations
# ---------------------------------------------------------------------------
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector, SparsePauliOp
import numpy as np
n = 4
qc = QuantumCircuit(n)
qc.h(0); qc.p(np.pi/2, 0)
qc.cx(0, 1); qc.cx(0, 2); qc.cx(0, 3) # GHZ+i state
sv = Statevector(qc)
# Pauli labels read right-to-left (rightmost char = qubit 0)
correlators = {'&lt;ZZ&gt;': 'IIZZ', '&lt;XX&gt;': 'IIXX', '&lt;YY&gt;': 'IIYY'}
print('Pairwise correlation functions (qubits 0,1) for the GHZ+i state:')
for label, pauli_str in correlators.items():
val = np.real(sv.expectation_value(SparsePauliOp(pauli_str)))
print(f' {label} = {val:+.4f}')
print('\nNote: &lt;ZZ&gt;=+1 confirms perfect classical Z-correlation, while')
print('&lt;XX&gt;=&lt;YY&gt;=0 shows that the phase coherence of a GHZ state is NOT')
print('visible in any two-qubit reduced pair -- only the full four-qubit')
print('correlators &lt;XXXX&gt;, &lt;YYYY&gt; reveal it (see Full Program).')</code></pre><p><strong>Expected Output:</strong></p><div class="box box-generic"><p>▶ Exp 11 — Expected Output &amp; Console Results</p><p>Pairwise correlation functions (qubits 0,1) for the GHZ+i state:</p><p>&lt;ZZ&gt; = +1.0000</p><p>&lt;XX&gt; = +0.0000</p><p>&lt;YY&gt; = +0.0000</p><p>Note: &lt;ZZ&gt;=+1 confirms perfect classical Z-correlation; &lt;XX&gt;=&lt;YY&gt;=0</p><p>shows the phase coherence is NOT visible in any two-qubit reduced</p><p>pair -- only the full four-qubit correlators reveal it.</p></div><h3 id="full-program-complete-version-9">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ───────────────────────────────────────────────────────────────────────
# Experiment 11: Expectation Value Sweeps and Quantum Correlations
# AQLL §8 | Dr. S. K. Jain, Invertis University, India
# ───────────────────────────────────────────────────────────────────────
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector, SparsePauliOp
import numpy as np, matplotlib.pyplot as plt
n = 4
def ghz_phase_state(theta):
qc = QuantumCircuit(n)
qc.h(0); qc.p(theta, 0)
qc.cx(0, 1); qc.cx(0, 2); qc.cx(0, 3)
return Statevector(qc)
pair_ops = {'&lt;ZZ&gt; (pair 0,1)': 'IIZZ', '&lt;XX&gt; (pair 0,1)': 'IIXX', '&lt;YY&gt; (pair 0,1)': 'IIYY'}
full_ops = {'&lt;ZZZZ&gt;': 'ZZZZ', '&lt;XXXX&gt;': 'XXXX', '&lt;YYYY&gt;': 'YYYY'}
thetas = np.linspace(0, 2*np.pi, 25)
pair_curves = {k: [] for k in pair_ops}
full_curves = {k: [] for k in full_ops}
for theta in thetas:
sv = ghz_phase_state(theta)
for label, p in pair_ops.items():
pair_curves[label].append(np.real(sv.expectation_value(SparsePauliOp(p))))
for label, p in full_ops.items():
full_curves[label].append(np.real(sv.expectation_value(SparsePauliOp(p))))
print('theta (rad) &lt;XXXX&gt; &lt;YYYY&gt; &lt;ZZZZ&gt;')
for i, theta in enumerate(thetas):
if i % 6 == 0:
print(f'{theta:8.4f} {full_curves["&lt;XXXX&gt;"][i]:+7.4f} '
f'{full_curves["&lt;YYYY&gt;"][i]:+7.4f} {full_curves["&lt;ZZZZ&gt;"][i]:+7.4f}')
# Analytic check: for 4 qubits &lt;XXXX&gt; = &lt;YYYY&gt; = cos(theta), &lt;ZZZZ&gt; = 1 always
analytic_xxxx = np.cos(thetas)
analytic_yyyy = np.cos(thetas)
max_err = max(np.max(np.abs(np.array(full_curves['&lt;XXXX&gt;']) - analytic_xxxx)),
np.max(np.abs(np.array(full_curves['&lt;YYYY&gt;']) - analytic_yyyy)))
print(f'\nMax deviation from analytic cos(theta) prediction: {max_err:.2e}')
print('Note: for n=4 qubits, i^4 = (-i)^4 = 1, so &lt;XXXX&gt; and &lt;YYYY&gt; coincide --')
print('unlike the 2-qubit Bell-state case where &lt;XX&gt; and &lt;YY&gt; have opposite sign.')
fig, axes = plt.subplots(1, 2, figsize=(13, 5))
axes[0].axhline(1.0, color='#4C72B0', label='&lt;ZZ&gt; pair (const = +1)')
axes[0].axhline(0.0, color='#C44E52', label='&lt;XX&gt;, &lt;YY&gt; pair (const = 0)')
axes[0].set_ylim(-1.2, 1.2)
axes[0].set_title('Pairwise Correlators vs Phase theta (no theta-dependence)')
axes[0].set_xlabel('theta (rad)'); axes[0].legend(fontsize=8)
axes[1].plot(thetas, full_curves['&lt;XXXX&gt;'], 'o-', label='&lt;XXXX&gt; (measured)')
axes[1].plot(thetas, full_curves['&lt;YYYY&gt;'], 's-', label='&lt;YYYY&gt; (measured)')
axes[1].plot(thetas, full_curves['&lt;ZZZZ&gt;'], '^-', label='&lt;ZZZZ&gt; (measured)')
axes[1].plot(thetas, analytic_xxxx, 'k--', linewidth=0.8, label='cos(theta) [analytic]')
axes[1].set_title('Full Four-Qubit Correlators vs Phase theta')
axes[1].set_xlabel('theta (rad)'); axes[1].set_ylabel('Expectation value')
axes[1].legend(fontsize=7)
plt.tight_layout()
plt.savefig('lab11_expectation_sweep_full.png', dpi=150)
plt.show()</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image57.png"/></figure><div class="box box-generic"><p>▶ Exp 11 — Expected Output &amp; Console Results</p><p>theta (rad) &lt;XXXX&gt; &lt;YYYY&gt; &lt;ZZZZ&gt;</p><p>0.0000 +1.0000 +1.0000 +1.0000</p><p>1.5708 +0.0000 +0.0000 +1.0000</p><p>3.1416 -1.0000 -1.0000 +1.0000</p><p>4.7124 -0.0000 -0.0000 +1.0000</p><p>6.2832 +1.0000 +1.0000 +1.0000</p><p>Max deviation from analytic cos(theta) prediction: 2.22e-16</p></div><h2 id="observation-and-results-10">3. Observation and Results</h2><h3 id="observation-tables-3">Observation Tables</h3><p><em>Complete the observation tables for Experiment 11. Fill in during the practical session using actual experimental data from AQLL and your own program.</em></p><table>
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
</table><h2 id="discussion-questions-10">4. Discussion Questions</h2><ul>
<li><p>Why do the two-qubit reduced correlators ⟨XX⟩ and ⟨YY⟩ vanish for the GHZ+i state, while the full four-qubit correlators ⟨XXXX⟩ and ⟨YYYY⟩ do not?</p></li>
<li><p>How would you experimentally distinguish the GHZ+i state from a classical 50/50 mixture of |0000⟩ and |1111⟩ using only expectation-value measurements?</p></li>
<li><p>What does it mean physically that ⟨ZZZZ⟩ stays fixed at +1 for every phase θ, while ⟨XXXX⟩ and ⟨YYYY⟩ vary with θ?</p></li>
<li><p>Write complete answers in your lab record with supporting calculations and diagrams.</p></li>
</ul><h2 id="lab-record-requirements-10">5. Lab Record Requirements</h2><ul>
<li><p>Run AQLL module for Experiment 11. Screenshot all output panels.</p></li>
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
<td><strong>What are the key quantum concepts demonstrated in Experiment 11?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Experiment 11 demonstrates: for GHZ+i, pairwise ⟨ZZ⟩=+1.0 but ⟨XX⟩=⟨YY⟩=0.0 since partial trace destroys coherence; the full four-qubit correlators ⟨XXXX⟩=⟨YYYY⟩=cos θ and ⟨ZZZZ⟩=+1.0 reveal genuine multipartite entanglement that no local hidden-variable model can reproduce. Angular sweep varies phase θ from 0 to 2π and tracks the resulting correlator evolution. Students should understand both the theoretical foundations and the practical Qiskit implementation, and be able to explain the significance of each result in terms of quantum information science principles covered in this manual.</td>
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
<td><strong>Why is measuring a full four-qubit operator like XXXX experimentally more demanding than measuring a two-qubit operator like ZZ?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Measuring a multi-qubit Pauli operator such as XXXX requires simultaneously rotating and reading out all four qubits in a consistent basis and combining their individual outcomes with the correct sign convention, whereas a two-qubit ZZ measurement only needs two qubits measured directly in the computational basis, making it comparatively simpler.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>What would you expect to observe if you performed this expectation-value sweep on a completely mixed (maximally decohered) 4-qubit state instead of GHZ+i?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>For a maximally mixed state, all expectation values of non-identity Pauli operators (including ⟨XXXX⟩, ⟨YYYY⟩, and ⟨ZZZZ⟩) would be zero, since a maximally mixed density matrix is proportional to the identity and Tr(ρO) vanishes for any traceless operator O.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>Why does the analytic prediction ⟨XXXX⟩ = ⟨YYYY⟩ = cosθ hold specifically for 4 qubits, and would it change for a different number of qubits?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>This coincidence arises because iⁿ = (−i)ⁿ = 1 when n = 4 (since i⁴ = 1), which makes the X and Y collective operators act identically on the GHZ superposition. For other qubit counts (e.g. n=2), the phase factors differ, giving ⟨YY⟩ = −cosθ instead — opposite in sign to ⟨XX⟩.</td>
</tr>
</tbody>
</table><p><strong>— GUIDED SIMULATION</strong></p><p><img loading="lazy" src="content/images/image58.png"/></p>