# math-game-app
import shutil
import os

# Prepare the folder path for the final version
project_name = "math-game-app-online"
project_path = f"/mnt/data/{project_name}"
pages_path = os.path.join(project_path, "pages")
public_assets_path = os.path.join(project_path, "public", "assets")

# Create necessary folders
os.makedirs(pages_path, exist_ok=True)
os.makedirs(public_assets_path, exist_ok=True)

# Create the index.tsx file
index_tsx_path = os.path.join(pages_path, "index.tsx")
index_code = """\
import React, { useState } from "react";
import useSound from "use-sound";

const correctSfx = "/assets/correct.mp3";
const wrongSfx = "/assets/wrong.mp3";
const winSfx = "/assets/win.mp3";
const mascotImg = "/assets/mascot.png";

const questionSets = {
  easy: [
    { question: "What is 2 + 1?", options: ["2", "3", "4", "5"], answer: "3" },
    { question: "What is 5 - 2?", options: ["1", "2", "3", "4"], answer: "3" },
  ],
  medium: [
    { question: "What is 8 + 6?", options: ["12", "14", "13", "15"], answer: "14" },
    { question: "What is 7 x 2?", options: ["12", "14", "16", "13"], answer: "14" },
  ],
  hard: [
    { question: "What is 36 ÷ 6?", options: ["5", "6", "7", "4"], answer: "6" },
    { question: "What is 12 x 3?", options: ["36", "32", "30", "28"], answer: "36" },
  ]
};

export default function Home() {
  const [level, setLevel] = useState(null);
  const [current, setCurrent] = useState(0);
  const [score, setScore] = useState(0);
  const [showScore, setShowScore] = useState(false);

  const [playCorrect] = useSound(correctSfx);
  const [playWrong] = useSound(wrongSfx);
  const [playWin] = useSound(winSfx);

  const questions = level ? questionSets[level] : [];

  const handleAnswer = (selected) => {
    if (selected === questions[current].answer) {
      setScore(score + 1);
      playCorrect();
    } else {
      playWrong();
    }
    const next = current + 1;
    if (next < questions.length) {
      setCurrent(next);
    } else {
      setShowScore(true);
      playWin();
    }
  };

  const getStars = () => {
    const total = questions.length;
    if (score === total) return "⭐⭐⭐ Great job!";
    if (score >= total / 2) return "⭐⭐ Keep going!";
    return "⭐ You can do it!";
  };

  if (!level) {
    return (
      <div style={{ textAlign: 'center', marginTop: '50px' }}>
        <h2>Choose Difficulty</h2>
        <button onClick={() => setLevel("easy")}>Easy</button>
        <button onClick={() => setLevel("medium")}>Medium</button>
        <button onClick={() => setLevel("hard")}>Hard</button>
      </div>
    );
  }

  return (
    <div style={{ maxWidth: '400px', margin: '40px auto' }}>
      <img src={mascotImg} alt="Mascot" width={80} style={{ display: 'block', margin: '0 auto' }} />
      {showScore ? (
        <div style={{ textAlign: 'center' }}>
          <h2>Well done!</h2>
          <p>Your score: {score} / {questions.length}</p>
          <p style={{ fontSize: '20px', color: 'gold' }}>{getStars()}</p>
        </div>
      ) : (
        <div>
          <h3>{questions[current].question}</h3>
          <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '10px' }}>
            {questions[current].options.map((option) => (
              <button key={option} onClick={() => handleAnswer(option)}>{option}</button>
            ))}
          </div>
        </div>
      )}
    </div>
  );
}
"""
with open(index_tsx_path, "w") as f:
    f.write(index_code)

# Create minimal package.json
package_json_path = os.path.join(project_path, "package.json")
with open(package_json_path, "w") as f:
    f.write("""{
  "name": "math-game-app",
  "version": "1.0.0",
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
  "dependencies": {
    "next": "14.1.0",
    "react": "18.2.0",
    "react-dom": "18.2.0",
    "use-sound": "^4.0.1"
  }
}
""")

# Zip for upload to GitHub or Vercel
zip_path = f"/mnt/data/{project_name}.zip"
shutil.make_archive(zip_path.replace(".zip", ""), 'zip', project_path)

zip_path
