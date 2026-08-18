<h1 id="experiment-9-quantum-fourier-transform-circuit-and-analysis">Experiment 9: Quantum Fourier Transform — Circuit and Analysis</h1><h2 id="background-theory-8">1. Background Theory</h2><p>QFT Definition: <img loading="lazy" src="content/images/image47.png"/> where N = 2ⁿ. For n=4: N=16, circuit depth O(n²) = O(16) gates vs classical FFT O(n·2ⁿ). Applied to |0000⟩: all 16 amplitudes = 1/4 = 0.25 (uniform). Phase staircase: for input |j⟩, phase of |k⟩ output is φ_k = 2πj·k/N. Circuit: n Hadamards + n(n-1)/2 controlled-phase gates (R_k = diag(1, e^{2πi/2^k})).</p><h2 id="qiskit-code-7">2. Qiskit Code</h2><h3 id="first-program-simple-version-8">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># ─────────────────────────────────────────────────────────────────────
# Experiment 9: Quantum Fourier Transform — 4 Qubits
# AQLL §7c | Dr. S. K. Jain, Invertis University, India
# ─────────────────────────────────────────────────────────────────────
from qiskit import QuantumCircuit
from qiskit.circuit.library import QFT
from qiskit.quantum_info import Statevector
import numpy as np, matplotlib.pyplot as plt
n = 4 # Number of qubits, N = 2^4 = 16
N = 2**n
# ── QFT applied to |0000⟩ ────────────────────────────────────────────
qc_0 = QuantumCircuit(n)
qc_0.append(QFT(n, inverse=False), range(n))
sv_0 = Statevector(qc_0)
print('QFT output amplitudes (input = |0000⟩):')
print(f'{'State':&gt;8} {'Re':&gt;8} {'Im':&gt;8} {'|amp|':&gt;8} {'Phase(rad)':&gt;12}')
for k, amp in enumerate(sv_0.data):
state_label = format(k, f'0{n}b')
phase = np.angle(amp)
print(f'|{state_label}&gt; {amp.real:&gt;8.5f} {amp.imag:&gt;8.5f} {abs(amp):&gt;8.5f} {phase:&gt;12.5f}')
expected_amp = 1/np.sqrt(N)
max_deviation = np.max(np.abs(np.abs(sv_0.data) - expected_amp))
print(f'\nExpected |amp| = {expected_amp:.6f} for all states')
print(f'Max deviation from uniform: {max_deviation:.2e} (should be &lt; 1e-14)')
# ── QFT applied to periodic state (|0⟩+|4⟩+|8⟩+|12⟩)/2 ─────────────
print('\n--- Period-4 input state ---')
psi_period4 = np.zeros(N, dtype=complex)
for j in [0, 4, 8, 12]: # Period = 4
psi_period4[j] = 0.5 # 1/2 normalisation (4 terms of magnitude 0.5: sum of |amp|^2 = 1)
# Build circuit that prepares this state (or just apply QFT to the vector)
from qiskit.quantum_info import Statevector as SV
sv_period = SV(psi_period4)
sv_qft_period = sv_period.evolve(QFT(n, inverse=False))
print('QFT of period-4 state: non-zero outputs at:')
for k, amp in enumerate(sv_qft_period.data):
if abs(amp) &gt; 0.01:
print(f' |{format(k,f"0{n}b")}&gt; (k={k:2d}): |amp|={abs(amp):.4f}, phase={np.angle(amp):.4f} rad')
# ── Draw QFT circuit ─────────────────────────────────────────────────
qc_draw = QuantumCircuit(n)
qc_draw.append(QFT(n, inverse=False, do_swaps=True).decompose(), range(n))
qc_draw.draw('mpl', filename='lab9_qft_circuit.png', fold=-1)
plt.show()</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image48.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image49.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image50.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image51.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image52.png"/></figure><h3 id="full-program-complete-version-7">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ───────────────────────────────────────────────────────────────────────
# Experiment 9: Quantum Fourier Transform — Circuit and Analysis
# AQLL §7c | Dr. S. K. Jain, Invertis University, India
# ───────────────────────────────────────────────────────────────────────
from qiskit import QuantumCircuit
from qiskit.circuit.library import QFT
from qiskit.quantum_info import Statevector
import numpy as np, matplotlib.pyplot as plt
n = 4 # Number of qubits, N = 2**4 = 16
N = 2**n
# ---- Part 1: QFT on several computational basis states |j&gt; ----------------
def qft_output(j):
qc = QuantumCircuit(n)
bits = format(j, f'0{n}b')
for q, b in enumerate(reversed(bits)):
if b == '1':
qc.x(q)
qc.append(QFT(n, inverse=False), range(n))
return Statevector(qc)
test_inputs = [0, 1, 4, 15]
phase_records = {}
for j in test_inputs:
sv = qft_output(j)
phases = np.angle(sv.data)
amps = np.abs(sv.data)
phase_records[j] = phases
print(f'\n--- QFT applied to |{format(j, f"0{n}b")}&gt; (j={j}) ---')
print(f'Max |amp| deviation from uniform 1/sqrt(N): '
f'{np.max(np.abs(amps - 1/np.sqrt(N))):.2e}')
# ---- Part 2: round-trip fidelity, QFT followed by inverse QFT -------------
qc_round = QuantumCircuit(n)
qc_round.h(0); qc_round.cx(0, 1); qc_round.p(np.pi/3, 2) # arbitrary test state
sv_before = Statevector(qc_round)
qc_round.append(QFT(n, inverse=False), range(n))
qc_round.append(QFT(n, inverse=True), range(n))
sv_after = Statevector(qc_round)
fidelity = abs(sv_before.inner(sv_after)) ** 2
print(f'\nRound-trip fidelity |&lt;psi|QFT^-1 QFT|psi&gt;|^2 = {fidelity:.10f}')
# ---- Part 3: QFT on periodic states (period 2, 4, 8) -- period finding ----
periods = [2, 4, 8]
period_peaks = {}
for r in periods:
psi = np.zeros(N, dtype=complex)
positions = list(range(0, N, r))
amp = 1/np.sqrt(len(positions))
for p in positions:
psi[p] = amp
sv_period = Statevector(psi).evolve(QFT(n, inverse=False))
peaks = [k for k, a in enumerate(sv_period.data) if abs(a) &gt; 0.05]
period_peaks[r] = peaks
print(f'\nPeriod r={r}: input has {len(positions)} nonzero terms; '
f'QFT output peaks at k = {peaks} (spacing = N/r = {N // r})')
# ---- Part 4: four-panel visualisation --------------------------------------
fig, axes = plt.subplots(2, 2, figsize=(12, 9))
sv0 = qft_output(0)
axes[0, 0].bar(range(N), np.abs(sv0.data), color='#4C72B0')
axes[0, 0].axhline(1/np.sqrt(N), color='red', linestyle='--', label='1/sqrt(N)')
axes[0, 0].set_title('QFT|0000&gt;: Uniform Amplitude Output')
axes[0, 0].set_xlabel('Basis state k'); axes[0, 0].set_ylabel('|amplitude|')
axes[0, 0].legend()
for j in test_inputs:
axes[0, 1].plot(range(N), phase_records[j], marker='o', markersize=3, label=f'j={j}')
axes[0, 1].set_title('Phase Staircase: phi_k = 2*pi*j*k/N')
axes[0, 1].set_xlabel('Output index k'); axes[0, 1].set_ylabel('Phase (rad)')
axes[0, 1].legend(fontsize=8)
for r in periods:
axes[1, 0].scatter(period_peaks[r], [r]*len(period_peaks[r]), s=60, label=f'period r={r}')
axes[1, 0].set_title('QFT Period Finding: Peaks at Multiples of N/r')
axes[1, 0].set_xlabel('Output index k'); axes[1, 0].set_ylabel('Input period r')
axes[1, 0].legend(fontsize=8)
axes[1, 1].bar(['QFT then IQFT'], [fidelity], color='#55A868')
axes[1, 1].set_ylim(0, 1.05)
axes[1, 1].set_title(f'Round-trip Fidelity = {fidelity:.6f}')
plt.tight_layout()
plt.savefig('lab9_qft_full_analysis.png', dpi=150)
plt.show()
print('\nSummary: QFT maps computational basis states to uniform-amplitude,')
print('phase-encoded superpositions; its inverse exactly undoes this transform,')
print("and periodic input states produce QFT outputs peaked at multiples of N/r --")
print("the core mechanism exploited by phase estimation and Shor's algorithm.")</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image53.png"/></figure><div class="box box-generic"><p>▶ Exp 9 — Expected Output &amp; Console Results</p><p>--- QFT applied to |0000&gt; (j=0) ---</p><p>Max |amp| deviation from uniform 1/sqrt(N): 8.33e-17</p><p>--- QFT applied to |0001&gt; (j=1) ---</p><p>Max |amp| deviation from uniform 1/sqrt(N): 1.39e-16</p><p>--- QFT applied to |0100&gt; (j=4) ---</p><p>Max |amp| deviation from uniform 1/sqrt(N): 8.33e-17</p><p>--- QFT applied to |1111&gt; (j=15) ---</p><p>Max |amp| deviation from uniform 1/sqrt(N): 1.39e-16</p><p>Round-trip fidelity |&lt;psi|QFT^-1 QFT|psi&gt;|^2 = 1.0000000000</p><p>Period r=2: input has 8 nonzero terms; QFT output peaks at k = [0, 8] (spacing = N/r = 8)</p><p>Period r=4: input has 4 nonzero terms; QFT output peaks at k = [0, 4, 8, 12] (spacing = N/r = 4)</p><p>Period r=8: input has 2 nonzero terms; QFT output peaks at k = [0, 2, 4, 6, 8, 10, 12, 14] (spacing = N/r = 2)</p></div><h2 id="observation-and-results-8">3. Observation and Results</h2><h3 id="observation-tables-1">Observation Tables</h3><p><em>Complete the observation tables for Experiment 9. Fill in during the practical session using actual experimental data from AQLL and your own program.</em></p><h3 id="table-9.1-qft-output-amplitudes-input-0000">Table 9.1 — QFT Output Amplitudes (Input = |0000⟩)</h3><table>
<colgroup>
<col style="width: 19%"/>
<col style="width: 9%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
<col style="width: 32%"/>
</colgroup>
<thead>
<tr class="header">
<th><strong>Basis State |k⟩</strong></th>
<th><strong>Index k</strong></th>
<th><strong>Expected |amplitude|</strong></th>
<th><strong>Measured |amplitude|</strong></th>
<th><strong>Phase φₖ (rad)</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>|0000⟩</td>
<td>0</td>
<td>0.25</td>
<td></td>
<td>0</td>
</tr>
<tr class="even">
<td>|0001⟩</td>
<td>1</td>
<td>0.25</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>|0010⟩</td>
<td>2</td>
<td>0.25</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>|0011⟩</td>
<td>3</td>
<td>0.25</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>|0100⟩</td>
<td>4</td>
<td>0.25</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>|0101⟩</td>
<td>5</td>
<td>0.25</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>...</td>
<td>...</td>
<td>0.25</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>|1111⟩</td>
<td>15</td>
<td>0.25</td>
<td></td>
<td></td>
</tr>
</tbody>
</table><table>
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
</table><h2 id="discussion-questions-8">4. Discussion Questions</h2><ul>
<li><p>Why does the QFT produce a uniform amplitude distribution when applied to a single computational basis state, and how does this differ from applying it to a periodic superposition?</p></li>
<li><p>Explain the role of the QFT inside Shor's factoring algorithm — specifically, why finding the period of a function helps factor an integer.</p></li>
<li><p>The QFT circuit depth is O(n²) compared with the classical Fast Fourier Transform's O(n·2ⁿ). Why does this NOT by itself imply an exponential quantum speed-up for every Fourier-related classical algorithm?</p></li>
<li><p>Write complete answers in your lab record with supporting calculations and diagrams.</p></li>
</ul><h2 id="lab-record-requirements-8">5. Lab Record Requirements</h2><ul>
<li><p>Run AQLL module for Experiment 9. Screenshot all output panels.</p></li>
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
<td><strong>What are the key quantum concepts demonstrated in Experiment 9?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Experiment 9 demonstrates: QFT Definition: |j⟩ → (1/√N) Σ_{k=0}^{N-1} e^{2πijk/N} |k⟩ where N = 2ⁿ. For n=4: N=16, circuit depth O(n²) = O(16) gates vs classical FFT O(n·2ⁿ). Applied to |0000⟩: all 16 amplitudes = 1/4 = 0.25 (uniform). Phase staircase: for input |j⟩, phase of |k⟩ output is φ_k = 2πj·k/N. Circuit: n Hadamards ... Students should understand both the theoretical foundations and the practical Qiskit implementation, and be able to explain the significance of each result in terms of quantum information science principles covered in this manual.</td>
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
<td><strong>Why does the QFT use complex phase factors e^{2πijk/N} instead of the real-valued cosine/sine basis used in the classical DFT?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The QFT is defined directly as the quantum analogue of the discrete Fourier transform, mapping computational basis states to complex-amplitude superpositions. The complex exponential form is what allows the transform to be implemented as a sequence of unitary Hadamard and controlled-phase gates acting reversibly on qubit amplitudes.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>What is the purpose of the controlled-phase gates R_k in the QFT circuit, and why does the number of them grow as n(n−1)/2?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Each controlled-phase gate R_k = diag(1, e^{2πi/2^k}) entangles a pair of qubits with a phase that depends on their relative bit positions, building up the correct e^{2πijk/N} phase factor for every output amplitude. Since every pair of qubits needs exactly one such controlled interaction, the total count is the number of qubit pairs, n(n−1)/2.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>How would the QFT output differ if the input state were a periodic superposition of period r instead of a single basis state?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Applying the QFT to a period-r periodic superposition constructively interferes at output indices that are multiples of N/r and destructively interferes elsewhere, producing sharp peaks spaced by N/r. This periodic-peak behaviour is exactly what phase estimation and Shor's algorithm exploit to recover the unknown period r.</td>
</tr>
</tbody>
</table>