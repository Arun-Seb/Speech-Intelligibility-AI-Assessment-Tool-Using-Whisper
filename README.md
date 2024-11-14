# Speech Intelligibility Assessment Tool

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Automated speech intelligibility assessment using AI-based transcription with Whisper Large V2.

## 📄 About

This implementation is inspired by the research paper:

**Zhang, V.W., Sebastian, A., & Monaghan, J.J.M. (2025).** *Automated Speech Intelligibility Assessment Using AI-Based Transcription in Children with Cochlear Implants, Hearing Aids, and Normal Hearing.* Journal of Clinical Medicine, 14, 5280. [https://doi.org/10.3390/jcm14155280](https://doi.org/10.3390/jcm14155280)

The tool uses the **Whisper Large V2** model from Hugging Face to automatically transcribe speech and evaluate intelligibility by comparing with naive listener transcriptions.

## ✨ Features

- 🎙️ **Automatic Speech Transcription** using Whisper Large V2 from Hugging Face
- 📊 **Word Error Rate (WER) Calculation** with detailed error analysis (substitutions, deletions, insertions)
- 📈 **Bland-Altman Analysis** for assessing agreement between AI and human listeners
- 📉 **Visualization Tools** for generating publication-quality plots
- 🔄 **Batch Processing** for multiple audio files
- 📝 **Comprehensive Reporting** with Excel outputs

## 🚀 Installation

### Prerequisites

- Python 3.8 or higher
- CUDA-capable GPU (optional, but recommended for faster processing)
- **Your own audio files and transcription data** (no dataset included in this repository)

### Install Dependencies

```bash
# Clone the repository
git clone https://github.com/yourusername/speech-intelligibility-tool.git
cd speech-intelligibility-tool

# Create virtual environment (recommended)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install required packages
pip install torch torchaudio transformers
pip install pandas numpy matplotlib seaborn openpyxl
```

### Alternative: Using requirements.txt

```bash
pip install -r requirements.txt
```

## ⚠️ Important Note

**No dataset is included in this repository.** Users must provide their own audio files and transcription data. The original dataset from Zhang et al. (2025) is not publicly available and was not included in this implementation. This repository provides only the tools and methodology for conducting similar analyses on your own data.

## 📁 Data Format

### Reference Transcriptions Excel File
Your reference file should have the following structure:

| audio_file | reference_text |
|------------|----------------|
| audio_001.wav | The boy walked to the table |
| audio_002.wav | That is a big bed |

### Naive Listener Transcriptions Excel File
Your listener file should include:

| audio_file | listener_1 | listener_2 | listener_3 |
|------------|------------|------------|------------|
| audio_001.wav | The boy walked to the table | The boy walked in a table | The boy walked at a table |
| audio_002.wav | That is a big bed | That is the big bed | That is a big bed |

## 💻 Usage

### Basic Usage

```python
from speech_intelligibility import SpeechIntelligibilityAssessor

# Initialize the assessor
assessor = SpeechIntelligibilityAssessor()

# Transcribe a single audio file
transcription = assessor.transcribe_audio("path/to/audio.wav")
print(f"Transcription: {transcription}")

# Calculate Word Error Rate
reference = "The boy walked to the table"
wer, errors = assessor.calculate_wer(reference, transcription)
print(f"WER: {wer:.2f}%")
print(f"Errors: {errors}")
```

### Batch Processing

```python
from speech_intelligibility import SpeechIntelligibilityAssessor, process_audio_folder

# Initialize assessor
assessor = SpeechIntelligibilityAssessor()

# Process entire folder
results_df = process_audio_folder(
    assessor=assessor,
    audio_folder="data/audio_files",
    reference_file="data/reference_transcriptions.xlsx",
    listener_file="data/naive_listener_transcriptions.xlsx",
    output_folder="results"
)
```

### Bland-Altman Analysis

```python
import numpy as np

# Your data
ai_scores = np.array([8, 9, 10, 7, 8])  # Correct words from AI
listener_scores = np.array([7, 9, 9, 8, 8])  # Correct words from listener

# Perform analysis
ba_results = assessor.bland_altman_analysis(
    ai_scores, 
    listener_scores,
    listener_name="Naive Listener 1"
)

# Generate plot
assessor.plot_bland_altman(
    ai_scores, 
    listener_scores,
    listener_name="Naive Listener 1",
    save_path="bland_altman_plot.png"
)
```

## 📊 Output

The tool generates:

1. **Excel File** (`transcription_results.xlsx`):
   - AI transcriptions
   - Naive listener transcriptions
   - WER for each transcription method
   - Correct word counts
   - Error breakdowns (substitutions, deletions, insertions)

2. **Bland-Altman Plots** (PNG files):
   - Visual comparison between AI and each naive listener
   - 95% limits of agreement
   - Mean difference indicators

3. **Console Output**:
   - Summary statistics
   - WER comparisons
   - Agreement metrics

## 📈 Metrics Explained

### Word Error Rate (WER)

WER is calculated as:

```
WER = (S + D + I) / N × 100
```

Where:
- **S** = Substitutions (words replaced)
- **D** = Deletions (words missing)
- **I** = Insertions (extra words)
- **N** = Total words in reference

**Interpretation:**
- < 25%: Good transcription quality
- 25-50%: Moderate quality
- > 50%: Poor quality

### Bland-Altman Analysis

Assesses agreement between two measurement methods by plotting:
- **Mean difference**: Average bias between methods
- **95% Limits of Agreement (LOA)**: Range within which 95% of differences should fall
- **Outside LOA %**: Proportion of measurements with poor agreement

## 🔬 Research Application

This tool is particularly useful for:

- **Clinical Research**: Evaluating speech outcomes in children with hearing loss
- **Speech-Language Pathology**: Monitoring intervention effectiveness
- **Audiological Studies**: Assessing cochlear implant and hearing aid outcomes
- **Developmental Studies**: Tracking speech intelligibility development

## 🎯 Model Information

**Whisper Large V2** is used for all transcriptions:
- **Source**: OpenAI via Hugging Face ([openai/whisper-large-v2](https://huggingface.co/openai/whisper-large-v2))
- **Architecture**: Transformer-based encoder-decoder
- **Training**: 680,000 hours of multilingual and multitask supervised data
- **Advantages**: Robust to accents, noise, and variable speech patterns

## ⚠️ Limitations

- Model trained primarily on adult speech; may have variable performance on children's speech
- Requires substantial computational resources (GPU recommended)
- First-time model download is ~3GB
- Performance may vary with speech clarity and recording quality

## 📚 Citation

If you use this tool in your research, please cite:

```bibtex
@article{zhang2025automated,
  title={Automated Speech Intelligibility Assessment Using AI-Based Transcription in Children with Cochlear Implants, Hearing Aids, and Normal Hearing},
  author={Zhang, Vicky W and Sebastian, Arun and Monaghan, Jessica },
  journal={Journal of Clinical Medicine},
  volume={14},
  number={15},
  pages={5280},
  year={2025},
  publisher={MDPI},
  doi={10.3390/jcm14155280}
}
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Original research by Zhang, Seastian et al. (2025) at National Acoustic Laboratories
- OpenAI for the Whisper model
- Hugging Face for model hosting and transformers library
- The speech-language pathology and audiology research community

## 📧 Contact

For questions or support, please open an issue on GitHub.

---

**Disclaimer**: This tool is for research purposes. Clinical decisions should not be made solely based on automated assessments. Always consult with qualified speech-language pathologists and audiologists for clinical evaluation and intervention planning.

**Data Note**: This repository does not include any audio files or transcription datasets. Users must provide their own data in the format specified in this documentation. The original dataset from Zhang et al. (2025) was collected under specific ethical approvals and is not publicly available.
