import React, { useState } from 'react';
import { Calculator, AlertTriangle, CheckCircle, BookOpen } from 'lucide-react';

export default function DSECalculator() {
  const [scores, setScores] = useState({
    // 英文四卷
    englishP1: '',
    englishP2: '',
    englishP3: '',
    englishP4: '',
    // 中文兩卷
    chineseP1: '',
    chineseP2: '',
    // 數學兩卷
    mathP1: '',
    mathP2: '',
    // 公民科
    citizenship: '達標',
    // 選修科
    elective1Subject: '',
    elective1Score: '',
    elective2Subject: '',
    elective2Score: '',
    elective3Subject: '',
    elective3Score: ''
  });

  const [results, setResults] = useState(null);
  const [warnings, setWarnings] = useState([]);

  // 等級2的最低分數線（參考歷年數據）
  const grade2Cutoff = {
    english: 35,
    chinese: 40,
    math: 38
  };

  const electiveSubjects = [
    'Biology', 'Chemistry', 'Physics', 'Economics', 
    'Chinese History', 'Geography', 'ICT', 'M1', 'M2'
  ];

  // 計算總分
  const calculateTotalScore = (subject, p1, p2, p3 = null, p4 = null) => {
    if (subject === 'english') {
      return p1 * 0.25 + p2 * 0.25 + p3 * 0.35 + p4 * 0.15;
    } else if (subject === 'chinese') {
      return p1 * 0.5 + p2 * 0.5;
    } else if (subject === 'math') {
      return p1 * 0.65 + p2 * 0.35;
    }
    return 0;
  };

  // 分數轉等級
  const scoreToGrade = (score) => {
    if (score >= 90) return { grade: '5**', points: 7 };
    if (score >= 80) return { grade: '5*', points: 6 };
    if (score >= 70) return { grade: '5', points: 5 };
    if (score >= 60) return { grade: '4', points: 4 };
    if (score >= 50) return { grade: '3', points: 3 };
    if (score >= 40) return { grade: '2', points: 2 };
    return { grade: '1', points: 1 };
  };

  // 計算學校加權平均分
  const calculateSchoolWeightedAverage = (chineseTotal, englishTotal, mathTotal, electives) => {
    // 核心科目 × 3
    const coreWeighted = (chineseTotal + englishTotal + mathTotal) * 3;
    
    // 選修科目 × 2（最多計算3科）
    const electiveWeighted = electives.reduce((sum, elective) => sum + elective.score * 2, 0);
    
    // 總分除以15
    return (coreWeighted + electiveWeighted) / 15;
  };

  const handleInputChange = (field, value) => {
    setScores(prev => ({ ...prev, [field]: value }));
  };

  const validateAndCalculate = () => {
    const newWarnings = [];
    
    // 計算核心科目總分
    const englishTotal = calculateTotalScore('english', 
      parseFloat(scores.englishP1) || 0,
      parseFloat(scores.englishP2) || 0,
      parseFloat(scores.englishP3) || 0,
      parseFloat(scores.englishP4) || 0
    );
    
    const chineseTotal = calculateTotalScore('chinese',
      parseFloat(scores.chineseP1) || 0,
      parseFloat(scores.chineseP2) || 0
    );
    
    const mathTotal = calculateTotalScore('math',
      parseFloat(scores.mathP1) || 0,
      parseFloat(scores.mathP2) || 0
    );

    // 檢查是否低於等級2分數線
    if (englishTotal < grade2Cutoff.english) {
      newWarnings.push('英文總分低於等級2分數線，建議放棄此科目');
    }
    if (chineseTotal < grade2Cutoff.chinese) {
      newWarnings.push('中文總分低於等級2分數線，建議放棄此科目');
    }
    if (mathTotal < grade2Cutoff.math) {
      newWarnings.push('數學總分低於等級2分數線，建議放棄此科目');
    }

    setWarnings(newWarnings);

    // 如果有警告，不進行計算
    if (newWarnings.length > 0) {
      setResults(null);
      return;
    }

    // 計算等級
    const englishGrade = scoreToGrade(englishTotal);
    const chineseGrade = scoreToGrade(chineseTotal);
    const mathGrade = scoreToGrade(mathTotal);

    // 處理選修科目
    const electives = [];
    if (scores.elective1Subject && scores.elective1Score) {
      const score = parseFloat(scores.elective1Score);
      electives.push({
        subject: scores.elective1Subject,
        score: score,
        ...scoreToGrade(score)
      });
    }
    if (scores.elective2Subject && scores.elective2Score) {
      const score = parseFloat(scores.elective2Score);
      electives.push({
        subject: scores.elective2Subject,
        score: score,
        ...scoreToGrade(score)
      });
    }
    if (scores.elective3Subject && scores.elective3Score) {
      const score = parseFloat(scores.elective3Score);
      electives.push({
        subject: scores.elective3Subject,
        score: score,
        ...scoreToGrade(score)
      });
    }

    // Best 5 計算
    const allSubjects = [
      { name: '中文', score: chineseTotal, ...chineseGrade },
      { name: '英文', score: englishTotal, ...englishGrade },
      { name: '數學', score: mathTotal, ...mathGrade },
      ...electives
    ];

    allSubjects.sort((a, b) => b.points - a.points);
    const best5 = allSubjects.slice(0, 5);
    const best5Total = best5.reduce((sum, subject) => sum + subject.score, 0);
    const best5Points = best5.reduce((sum, subject) => sum + subject.points, 0);
    
    // 計算學校加權平均分：(中+英+數) × 3 + 選修科 × 2 然後除以15
    const schoolWeightedAverage = calculateSchoolWeightedAverage(chineseTotal, englishTotal, mathTotal, electives);

    setResults({
      coreSubjects: {
        chinese: { total: chineseTotal, ...chineseGrade },
        english: { total: englishTotal, ...englishGrade },
        math: { total: mathTotal, ...mathGrade }
      },
      electives,
      best5,
      best5Total: best5Total.toFixed(1),
      best5Points,
      schoolWeightedAverage: schoolWeightedAverage.toFixed(1),
      citizenship: scores.citizenship
    });
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-4">
      <div className="max-w-4xl mx-auto">
        <div className="bg-white rounded-lg shadow-lg p-6 mb-6">
          <div className="flex items-center gap-3 mb-6">
            <Calculator className="w-8 h-8 text-blue-600" />
            <h1 className="text-3xl font-bold text-gray-800">DSE成績計算器</h1>
          </div>
          <p className="text-gray-600 mb-8">基於歷年Cut Off數據，支援分卷計分</p>

          {/* 警告訊息 */}
          {warnings.length > 0 && (
            <div className="bg-red-50 border border-red-200 rounded-lg p-4 mb-6">
              <div className="flex items-center gap-2 mb-2">
                <AlertTriangle className="w-5 h-5 text-red-600" />
                <h3 className="font-semibold text-red-800">注意事項</h3>
              </div>
              {warnings.map((warning, index) => (
                <p key={index} className="text-red-700 text-sm">{warning}</p>
              ))}
            </div>
          )}

          {/* 英文科目 */}
          <div className="bg-blue-50 rounded-lg p-6 mb-6">
            <h2 className="text-xl font-semibold text-blue-800 mb-4 flex items-center gap-2">
              <BookOpen className="w-5 h-5" />
              英文科（滿分100分）
            </h2>
            <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">
                  卷一 (25%)
                </label>
                <input
                  type="number"
                  min="0"
                  max="100"
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                  value={scores.englishP1}
                  onChange={(e) => handleInputChange('englishP1', e.target.value)}
                />
              </div>
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">
                  卷二 (25%)
                </label>
                <input
                  type="number"
                  min="0"
                  max="100"
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                  value={scores.englishP2}
                  onChange={(e) => handleInputChange('englishP2', e.target.value)}
                />
              </div>
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">
                  卷三 (35%)
                </label>
                <input
                  type="number"
                  min="0"
                  max="100"
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                  value={scores.englishP3}
                  onChange={(e) => handleInputChange('englishP3', e.target.value)}
                />
              </div>
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">
                  卷四 (15%)
                </label>
                <input
                  type="number"
                  min="0"
                  max="100"
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                  value={scores.englishP4}
                  onChange={(e) => handleInputChange('englishP4', e.target.value)}
                />
              </div>
            </div>
          </div>

          {/* 中文科目 */}
          <div className="bg-red-50 rounded-lg p-6 mb-6">
            <h2 className="text-xl font-semibold text-red-800 mb-4 flex items-center gap-2">
              <BookOpen className="w-5 h-5" />
              中文科（滿分100分）
            </h2>
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">
                  卷一 (50%)
                </label>
                <input
                  type="number"
                  min="0"
                  max="100"
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-red-500"
                  value={scores.chineseP1}
                  onChange={(e) => handleInputChange('chineseP1', e.target.value)}
                />
              </div>
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">
                  卷二 (50%)
                </label>
                <input
                  type="number"
                  min="0"
                  max="100"
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-red-500"
                  value={scores.chineseP2}
                  onChange={(e) => handleInputChange('chineseP2', e.target.value)}
                />
              </div>
            </div>
          </div>

          {/* 數學科目 */}
          <div className="bg-green-50 rounded-lg p-6 mb-6">
            <h2 className="text-xl font-semibold text-green-800 mb-4 flex items-center gap-2">
              <BookOpen className="w-5 h-5" />
              數學科（滿分100分）
            </h2>
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">
                  卷一 (65%)
                </label>
                <input
                  type="number"
                  min="0"
                  max="100"
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500"
                  value={scores.mathP1}
                  onChange={(e) => handleInputChange('mathP1', e.target.value)}
                />
              </div>
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">
                  卷二 (35%)
                </label>
                <input
                  type="number"
                  min="0"
                  max="100"
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500"
                  value={scores.mathP2}
                  onChange={(e) => handleInputChange('mathP2', e.target.value)}
                />
              </div>
            </div>
          </div>

          {/* 公民科 */}
          <div className="bg-purple-50 rounded-lg p-6 mb-6">
            <h2 className="text-xl font-semibold text-purple-800 mb-4">公民與社會發展科</h2>
            <select
              className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-purple-500"
              value={scores.citizenship}
              onChange={(e) => handleInputChange('citizenship', e.target.value)}
            >
              <option value="達標">達標</option>
              <option value="未達標">未達標</option>
            </select>
          </div>

          {/* 選修科目 */}
          <div className="bg-orange-50 rounded-lg p-6 mb-6">
            <h2 className="text-xl font-semibold text-orange-800 mb-4">選修科目</h2>
            {[1, 2, 3].map((num) => (
              <div key={num} className="mb-4 grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">
                    選修{num}科目
                  </label>
                  <select
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-orange-500"
                    value={scores[`elective${num}Subject`]}
                    onChange={(e) => handleInputChange(`elective${num}Subject`, e.target.value)}
                  >
                    <option value="">請選擇科目</option>
                    {electiveSubjects.map(subject => (
                      <option key={subject} value={subject}>{subject}</option>
                    ))}
                  </select>
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">
                    分數 (0-100)
                  </label>
                  <input
                    type="number"
                    min="0"
                    max="100"
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-orange-500"
                    value={scores[`elective${num}Score`]}
                    onChange={(e) => handleInputChange(`elective${num}Score`, e.target.value)}
                  />
                </div>
              </div>
            ))}
          </div>

          <button
            onClick={validateAndCalculate}
            className="w-full bg-blue-600 text-white py-3 px-6 rounded-lg font-semibold hover:bg-blue-700 transition duration-200"
          >
            計算成績
          </button>
        </div>

        {/* 結果顯示 */}
        {results && (
          <div className="bg-white rounded-lg shadow-lg p-6">
            <h2 className="text-2xl font-bold text-gray-800 mb-6 flex items-center gap-2">
              <CheckCircle className="w-6 h-6 text-green-600" />
              計算結果
            </h2>

            {/* 核心科目結果 */}
            <div className="mb-6">
              <h3 className="text-lg font-semibold text-gray-700 mb-3">核心科目成績</h3>
              <div className="space-y-2">
                <div className="flex justify-between items-center p-3 bg-gray-50 rounded">
                  <span>中文：{results.coreSubjects.chinese.total.toFixed(1)}分</span>
                  <span className="font-semibold text-blue-600">{results.coreSubjects.chinese.grade}</span>
                </div>
                <div className="flex justify-between items-center p-3 bg-gray-50 rounded">
                  <span>英文：{results.coreSubjects.english.total.toFixed(1)}分</span>
                  <span className="font-semibold text-blue-600">{results.coreSubjects.english.grade}</span>
                </div>
                <div className="flex justify-between items-center p-3 bg-gray-50 rounded">
                  <span>數學：{results.coreSubjects.math.total.toFixed(1)}分</span>
                  <span className="font-semibold text-blue-600">{results.coreSubjects.math.grade}</span>
                </div>
                <div className="flex justify-between items-center p-3 bg-gray-50 rounded">
                  <span>公民與社會發展科</span>
                  <span className="font-semibold text-green-600">{results.citizenship}</span>
                </div>
              </div>
            </div>

            {/* 選修科目結果 */}
            {results.electives.length > 0 && (
              <div className="mb-6">
                <h3 className="text-lg font-semibold text-gray-700 mb-3">選修科目成績</h3>
                <div className="space-y-2">
                  {results.electives.map((elective, index) => (
                    <div key={index} className="flex justify-between items-center p-3 bg-gray-50 rounded">
                      <span>{elective.subject}：{elective.score}分</span>
                      <span className="font-semibold text-blue-600">{elective.grade}</span>
                    </div>
                  ))}
                </div>
              </div>
            )}

            {/* Best 5 結果 */}
            <div className="bg-gradient-to-r from-blue-50 to-indigo-50 rounded-lg p-6">
              <h3 className="text-lg font-semibold text-gray-700 mb-4">Best 5 計算結果</h3>
              <div className="space-y-3">
                <div className="flex justify-between">
                  <span>Best 5 原始總分：</span>
                  <span className="font-bold text-blue-600">{results.best5Total}分</span>
                </div>
                <div className="flex justify-between">
                  <span>Best 5 等級總分（大學計分）：</span>
                  <span className="font-bold text-blue-600">{results.best5Points}分</span>
                </div>
                <div className="flex justify-between">
                  <span>學校加權平均分：</span>
                  <span className="font-bold text-blue-600">{results.schoolWeightedAverage}分</span>
                </div>
                <div className="mt-4 p-3 bg-white rounded border">
                  <p className="text-sm text-gray-600 mb-2">Best 5 等級組合：</p>
                  <p className="font-semibold text-gray-800">
                    {results.best5.map(subject => subject.grade).join(' / ')}
                  </p>
                </div>
              </div>
            </div>

            <div className="mt-6 p-4 bg-yellow-50 rounded-lg">
              <p className="text-sm text-yellow-800 mb-2">
                <strong>注：</strong>大學入學計分標準（5**=7分，5*=6分，5=5分，4=4分，3=3分，2=2分，1=1分）
              </p>
              <p className="text-sm text-yellow-800">
                <strong>學校加權平均分計算：</strong>（中文+英文+數學）×3 + 選修科×2 然後除以15
              </p>
            </div>
          </div>
        )}
      </div>
    </div>
  );
}
