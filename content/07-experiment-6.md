<h1 id="experiment-6-sampling-laboratory-shot-noise-and-statistical-convergence">Experiment 6: Sampling Laboratory — Shot Noise and Statistical Convergence</h1><h2 id="background-theory-5">1. Background Theory</h2><p>Born Rule and Shot Noise: <img loading="lazy" src="content/images/image29.png"/>. Shot noise formula: <img loading="lazy" src="content/images/image30.png"/>. At p=0.5, N=100: σ=0.050. At N=1000: σ=0.016. At N=10000: σ=0.005. The convergence σ ∝ 1/√N means halving σ requires 4× more shots. The GHZ+i state gives exactly two outcomes (|0000⟩ and |1111⟩, each with probability 0.5), making it ideal for studying shot noise.</p><h2 id="qiskit-code-4">2. Qiskit Code</h2><h3 id="first-program-simple-version-5">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># -----------------------------------------------------------------------
# Experiment 6 — First Program: Sampling and Shot Noise (simple version)
# Dr. S. K. Jain, India
#
# REQUIRES: qiskit &gt;= 1.0 AND qiskit-aer &gt;= 0.14
# If you see an ImportError, run: pip install qiskit qiskit-aer --upgrade
# -----------------------------------------------------------------------
import sys
# ── version guard ────────────────────────────────────────────────────────────
try:
import qiskit
import qiskit_aer
from packaging.version import Version
qiskit_ver = Version(qiskit.__version__)
aer_ver = Version(qiskit_aer.__version__)
if qiskit_ver &lt; Version("1.0"):
sys.exit(f"[ERROR] Qiskit {qiskit.__version__} is too old. "
"Run: pip install qiskit --upgrade")
if aer_ver &lt; Version("0.14"):
sys.exit(f"[ERROR] qiskit-aer {qiskit_aer.__version__} is too old. "
"Run: pip install qiskit-aer --upgrade")
except ImportError as e:
sys.exit(f"[ERROR] Missing package: {e}\n"
"Run: pip install qiskit qiskit-aer packaging --upgrade")
# ─────────────────────────────────────────────────────────────────────────────
from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator
import numpy as np
# ── Build the circuit ────────────────────────────────────────────────────────
# H + Phase(π/2) on qubit 0 → |+i⟩ (equal superposition with phase)
# Three CNOT gates entangle qubits 1-3 with qubit 0
# Result: equal probability of |0000⟩ and |1111⟩ (p ≈ 0.5 each)
qc = QuantumCircuit(4, 4)
qc.h(0)
qc.p(np.pi / 2, 0)
qc.cx(0, 1)
qc.cx(0, 2)
qc.cx(0, 3)
qc.measure(range(4), range(4))
print("Circuit diagram:")
print(qc.draw(output="text"))
print()
# ── Run and analyse shot noise ───────────────────────────────────────────────
sim = AerSimulator()
print(f"{'Shots':&gt;8} {'P(0000)':&gt;8} {'σ_theory':&gt;10} {'|deviation|':&gt;12}")
print("-" * 46)
for shots in [100, 1_000, 10_000]:
counts = sim.run(qc, shots=shots).result().get_counts()
p_0000 = counts.get("0000", 0) / shots
sigma = np.sqrt(0.5 * 0.5 / shots) # binomial std dev for p=0.5
dev = abs(p_0000 - 0.5)
print(f"{shots:&gt;8d} {p_0000:&gt;8.4f} {sigma:&gt;10.4f} {dev:&gt;12.4f}")
print()
print("As shot count increases, P(0000) converges to 0.5 and σ shrinks — shot noise ∝ 1/√N.")</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image31.png"/></figure><h3 id="full-program-complete-version-4">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ==============================================================
# Full Program: Complete Sampling Analysis with Chi-squared test
# Dr. S. K. Jain, India
# ==============================================================
from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator
from scipy import stats
import numpy as np, matplotlib.pyplot as plt
qc = QuantumCircuit(4, 4)
qc.h(0); qc.p(np.pi/2, 0)
qc.cx(0,1); qc.cx(0,2); qc.cx(0,3)
qc.measure(range(4), range(4))
sim = AerSimulator()
shot_list = [100, 500, 1000, 5000, 10000, 50000]
ideal_p = 0.5
print(f'{"Shots":&gt;8} {"P(0000)":&gt;10} {"P(1111)":&gt;10} {"sigma_th":&gt;10} {"|dev|":&gt;10} {"chi2 p":&gt;10}')
for shots in shot_list:
counts = sim.run(qc, shots=shots).result().get_counts()
total = sum(counts.values())
p_0000 = counts.get('0000', 0) / total
p_1111 = counts.get('1111', 0) / total
sigma_theory = np.sqrt(ideal_p * (1-ideal_p) / shots)
obs_dev = abs(p_0000 - ideal_p)
chi2, p_val = stats.chisquare([counts.get('0000',0), counts.get('1111',0)],
f_exp=[shots/2, shots/2])
print(f'{shots:&gt;8} {p_0000:&gt;10.4f} {p_1111:&gt;10.4f} {sigma_theory:&gt;10.4f} {obs_dev:&gt;10.4f} {p_val:&gt;10.4f}')
# Plot convergence
fig, axes = plt.subplots(2, 3, figsize=(18, 10))
for idx, shots in enumerate(shot_list):
counts = sim.run(qc, shots=shots).result().get_counts()
total = sum(counts.values())
all_states = sorted(counts.keys())
probs = [counts.get(s,0)/total for s in all_states]
ax = axes[idx//3, idx%3]
ax.bar(range(len(all_states)), probs, color='#2E75B6', edgecolor='white')
ax.axhline(0.5, color='red', ls='--', lw=2, label='Ideal 0.5')
ax.set_title(f'{shots} shots', fontweight='bold')
ax.set_ylim(0, 1.0); ax.legend(fontsize=8)
plt.suptitle('Born Rule and Shot Noise: Statistical Convergence', fontsize=14, fontweight='bold')
plt.tight_layout()
plt.savefig('lab6_shot_noise.png', dpi=150, bbox_inches='tight')
plt.show()</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image32.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image33.png"/></figure><pre><code>▶ Exp 6 — Expected Output &amp; Console Results
CONSOLE OUTPUT (typical values):
Shots P(0000) P(1111) sigma_th |dev| chi2 p
100 0.480 0.520 0.0500 0.020 0.721
1000 0.503 0.497 0.0158 0.003 0.881
10000 0.4996 0.5004 0.0050 0.0004 0.936
50000 0.50001 0.49999 0.0022 0.00001 0.998
KEY OBSERVATION: Shot noise σ ∝ 1/√N
Log-log slope of σ vs N = -0.5 exactly (convergence rate)</code></pre><h2 id="observation-and-results-5">3. Observation and Results</h2><h3 id="table-6.1-shot-noise-vs-number-of-measurements">Table 6.1 — Shot Noise vs Number of Measurements</h3><table>
<colgroup>
<col style="width: 11%"/>
<col style="width: 14%"/>
<col style="width: 14%"/>
<col style="width: 20%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Shots N</strong></td>
<td><strong>P(|0000⟩)</strong></td>
<td><strong>P(|1111⟩)</strong></td>
<td><strong>σ_theory = √(0.25/N)</strong></td>
<td><strong>Observed |deviation|</strong></td>
<td><strong>χ² p-value</strong></td>
</tr>
<tr class="even">
<td>100</td>
<td></td>
<td></td>
<td>0.0500</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>500</td>
<td></td>
<td></td>
<td>0.0224</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>1,000</td>
<td></td>
<td></td>
<td>0.0158</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>5,000</td>
<td></td>
<td></td>
<td>0.0071</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>10,000</td>
<td></td>
<td></td>
<td>0.0050</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>50,000</td>
<td></td>
<td></td>
<td>0.0022</td>
<td></td>
<td></td>
</tr>
</tbody>
</table><h2 id="discussion-questions-5">4. Discussion Questions</h2><ul>
<li><p>Plot the theoretical shot noise σ = √(0.25/N) vs N on log-log axes. What is the slope? What does this imply about the rate of convergence?</p></li>
<li><p>At 100 shots, what is the probability that BOTH |0000⟩ and |1111⟩ each appear exactly 50 times? Use the binomial distribution.</p></li>
<li><p>Why does quantum measurement require many shots while quantum algorithms are often described as "one-query" algorithms?</p></li>
</ul><h2 id="lab-record-requirements-5">5. Lab Record Requirements</h2><ul>
<li><p>Run AQLL §6. Screenshot the S6_sampling_shots.png output.</p></li>
<li><p>Run the First Program. Record P(|0000⟩) for each shot count.</p></li>
<li><p>Run the Full Program. Record all values in Table 6.1.</p></li>
<li><p>Complete Table 6.1. Verify that chi-squared p-value &gt; 0.05 (consistent with Born rule).</p></li>
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
<td><strong>What is the Born rule and why is it fundamental to quantum mechanics?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The Born rule: P(outcome k) = |⟨k|ψ⟩|² = Tr(|k⟩⟨k|·ρ). It provides the bridge between abstract quantum amplitudes and experimentally observable probabilities. It cannot be derived from more fundamental principles within standard quantum mechanics — it is an axiom. The Born rule ensures that probabilities sum to 1 (since Σ_k|⟨k|ψ⟩|² = ⟨ψ|ψ⟩ = 1) and provides the only probabilistic interpretation consistent with quantum linearity.</td>
</tr>
<tr class="even">
<td><strong>Q2</strong></td>
<td><strong>Derive the shot noise formula σ = √(p(1−p)/N).</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Each shot is an independent Bernoulli trial with probability p. For N shots, the count X follows Binomial(N,p): E[X]=Np, Var(X)=Np(1−p). The estimated probability p̂=X/N has: E[p̂]=p, Var(p̂)=Var(X/N)=Np(1−p)/N²=p(1−p)/N. Standard deviation σ(p̂)=√(p(1−p)/N). At p=0.5 (GHZ case): σ=0.5/√N. For N=100: σ=0.05; N=10000: σ=0.005.</td>
</tr>
<tr class="even">
<td><strong>Q3</strong></td>
<td><strong>What is a chi-squared test and what does the p-value tell you?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Chi-squared test compares observed frequencies with expected frequencies. Test statistic: χ² = Σᵢ (Oᵢ−Eᵢ)²/Eᵢ. p-value = P(χ² ≥ observed | distribution is correct). p-value &gt; 0.05 means the data is consistent with the theoretical distribution (fail to reject null hypothesis). For our GHZ sampling: p &gt;&gt; 0.05 confirms Born rule compliance.</td>
</tr>
<tr class="even">
<td><strong>Q4</strong></td>
<td><strong>Why does quantum measurement require multiple shots?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Quantum measurement collapses the state to ONE definite outcome randomly. Each shot is an independent sample from the Born rule probability distribution. To estimate P(outcome k) = |⟨k|ψ⟩|² to precision ε requires N ≈ 1/ε² shots. "One-query" algorithms like Grover's exploit coherent interference internally but still need ≥ 1 shot to read the answer. The quantum speedup is in the number of oracle queries, not the number of measurements.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>What is the relationship between quantum sampling and classical simulation?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Simulating quantum sampling classically requires computing all 2ⁿ amplitudes (exponential space and time). Google's quantum supremacy claim (2019): 53-qubit Sycamore processor sampled from a random circuit distribution in 200 seconds, estimated to take 10,000 years on classical supercomputers. The hardness of quantum sampling is related to complexity classes BosonSampling and IQP (Instantaneous Quantum Polynomial-time circuits).</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>Why is the standard deviation of the sample proportion given by √[p(1−p)/N] rather than just 1/√N?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>This is the binomial standard error: for N independent Bernoulli trials with success probability p, the variance of the sample proportion is p(1−p)/N, so the standard deviation is its square root. It equals 1/(2√N) only at the worst case p=0.5; for other p it is smaller, reflecting less uncertainty when outcomes are less balanced.</td>
</tr>
<tr class="even">
<td><strong>Q7</strong></td>
<td><strong>Why does the GHZ+i circuit give exactly two outcomes with probability 0.5 each, rather than a spread over many outcomes?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The Hadamard and phase gate put qubit 0 into an equal superposition of |0⟩ and |1⟩, and the three CNOTs copy that single bit of randomness onto qubits 1, 2, and 3. Because every qubit ends up correlated with qubit 0, only the all-zero and all-one bitstrings are possible, each occurring with the same probability as the original H-gate superposition, i.e. 0.5.</td>
</tr>
<tr class="even">
<td><strong>Q8</strong></td>
<td><strong>What would happen to the shot-noise curve if you used a biased qubit state (e.g. p=0.1) instead of p=0.5?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The standard deviation √[p(1−p)/N] would be smaller for a given N because p(1−p) is maximised at p=0.5 and decreases toward the extremes. Convergence to the true probability would therefore appear “tighter” and require fewer shots to reach the same absolute precision.</td>
</tr>
<tr class="even">
<td><strong>Q9</strong></td>
<td><strong>Why does the chi-squared test use expected counts of shots/2 for each outcome rather than the observed counts?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The chi-squared goodness-of-fit test compares observed counts against what theory predicts under the null hypothesis (here, a perfectly fair 50/50 split). Using the observed counts as the expectation would make the test trivially pass regardless of the data, since it would just be comparing the data to itself.</td>
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
<p><strong>7</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Quantum Teleportation Protocol — Complete Analysis</strong></p>
<p>AQLL §7a | Phase 1: Guided Simulation</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Implement and verify the complete quantum teleportation protocol; confirm teleportation fidelity = 1 for arbitrary input states; understand why teleportation does not violate causality.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>AQLL §7a | Phase 1: Guided Simulation</td>
</tr>
</tbody>
</table>