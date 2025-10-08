import React, { useState, useEffect } from 'react'
export default function TaskManager() {
  const [tasks, setTasks] = useState(() => {
    try {
      return JSON.parse(localStorage.getItem('tasks') || '[]')
    } catch { return [] }
  })
  const [text, setText] = useState('')
  const [filter, setFilter] = useState('all')
  const [editingId, setEditingId] = useState(null)
  const [editText, setEditText] = useState('')
  useEffect(() => { localStorage.setItem('tasks', JSON.stringify(tasks)) }, [tasks])
  function addTask(e) {
    e.preventDefault()
    if (!text.trim()) return
    const task = { id: Date.now().toString(36) + Math.random().toString(36).slice(2), text: text.trim(), done: false, createdAt: Date.now() }
    setTasks(prev => [task, ...prev])
    setText('')
  }
  function toggleDone(id) { setTasks(prev => prev.map(t => t.id === id ? { ...t, done: !t.done } : t)) }
  function removeTask(id) { setTasks(prev => prev.filter(t => t.id !== id)) }
  function startEdit(t) { setEditingId(t.id); setEditText(t.text) }
  function saveEdit(e) { e.preventDefault(); if (!editText.trim()) return; setTasks(prev => prev.map(t => t.id === editingId ? { ...t, text: editText.trim() } : t)); setEditingId(null); setEditText('') }
  function cancelEdit() { setEditingId(null); setEditText('') }
  function clearCompleted() { setTasks(prev => prev.filter(t => !t.done)) }
  const filtered = tasks.filter(t => filter === 'all' ? true : filter === 'active' ? !t.done : t.done)
  return (
    <div className="min-h-screen bg-slate-50 flex items-center justify-center p-4">
      <div className="w-full max-w-2xl bg-white shadow-lg rounded-2xl p-6">
        <div className="flex items-center justify-between mb-4">
          <h1 className="text-2xl font-semibold">Task Manager</h1>
          <div className="text-sm text-slate-500">{tasks.length} tasks</div>
        </div>
        <form onSubmit={addTask} className="flex gap-2 mb-4">
          <input value={text} onChange={e => setText(e.target.value)} placeholder="Add a new task" className="flex-1 p-3 rounded-lg border border-slate-200 focus:outline-none focus:ring-2 focus:ring-indigo-300" />
          <button className="px-4 py-3 bg-indigo-600 text-white rounded-lg">Add</button>
        </form>
        <div className="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-3 mb-4">
          <div className="flex items-center gap-2">
            <button onClick={() => setFilter('all')} className={`px-3 py-2 rounded-lg ${filter==='all'?'bg-indigo-100':''}`}>All</button>
            <button onClick={() => setFilter('active')} className={`px-3 py-2 rounded-lg ${filter==='active'?'bg-indigo-100':''}`}>Active</button>
            <button onClick={() => setFilter('completed')} className={`px-3 py-2 rounded-lg ${filter==='completed'?'bg-indigo-100':''}`}>Completed</button>
          </div>
          <div className="flex items-center gap-2 text-sm text-slate-600">
            <span>{tasks.filter(t=>!t.done).length} left</span>
            <button onClick={clearCompleted} className="ml-2 px-3 py-2 rounded-lg bg-slate-100">Clear completed</button>
          </div>
        </div>
        <ul className="space-y-2">
          {filtered.length === 0 && <li className="text-center text-slate-400 py-6">No tasks</li>}
          {filtered.map(t => (
            <li key={t.id} className="flex items-center justify-between p-3 border rounded-lg border-slate-100">
              <div className="flex items-center gap-3">
                <input type="checkbox" checked={t.done} onChange={() => toggleDone(t.id)} className="w-5 h-5" />
                <div>
                  <div className={`select-none ${t.done?'line-through text-slate-400':''}`}>{t.text}</div>
                  <div className="text-xs text-slate-400">{new Date(t.createdAt).toLocaleString()}</div>
                </div>
              </div>
              <div className="flex items-center gap-2">
                <button onClick={() => startEdit(t)} className="px-3 py-1 rounded-md bg-slate-100">Edit</button>
                <button onClick={() => removeTask(t.id)} className="px-3 py-1 rounded-md bg-rose-100">Delete</button>
              </div>
            </li>
          ))}
        </ul>
        {editingId && (
          <div className="fixed inset-0 bg-black/30 flex items-center justify-center p-4">
            <form onSubmit={saveEdit} className="w-full max-w-lg bg-white p-4 rounded-xl shadow-lg">
              <div className="flex gap-2">
                <input value={editText} onChange={e=>setEditText(e.target.value)} className="flex-1 p-3 border rounded-lg" />
                <button className="px-4 py-3 bg-indigo-600 text-white rounded-lg">Save</button>
                <button type="button" onClick={cancelEdit} className="px-4 py-3 bg-slate-200 rounded-lg">Cancel</button>
              </div>
            </form>
          </div>
        )}
        <div className="mt-6 text-center text-xs text-slate-400">Responsive simple task manager · built with React</div>
      </div>
    </div>
  )
}
