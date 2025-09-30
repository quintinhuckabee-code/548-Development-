# 548-Development-
This is to help keep track of all project that we have under contruction or pre development
import React, { useState } from 'react';
import { Plus, X, ChevronDown, ChevronUp } from 'lucide-react';

const ProjectManagerTracker = () => {
  const [activeTab, setActiveTab] = useState('dashboard');
  const [projects, setProjects] = useState([
    {
      id: 1,
      name: 'Downtown Mixed-Use Development',
      status: 'In Progress',
      budget: 5000000,
      startDate: '2025-01-15',
      targetDate: '2026-06-30',
      phase: 'Construction'
    }
  ]);

  const [costs, setCosts] = useState([
    { id: 1, projectId: 1, category: 'Hard Cost', subcategory: 'Site Work', description: 'Excavation and Grading', budgeted: 250000, actual: 235000, vendor: 'Smith Excavating', date: '2025-02-01', phase: 'Site Prep' },
    { id: 2, projectId: 1, category: 'Soft Cost', subcategory: 'Architecture', description: 'Design Development', budgeted: 150000, actual: 150000, vendor: 'ABC Architects', date: '2025-01-20', phase: 'Design' },
    { id: 3, projectId: 1, category: 'Hard Cost', subcategory: 'Foundation', description: 'Concrete Foundation', budgeted: 400000, actual: 385000, vendor: 'Concrete Co', date: '2025-03-15', phase: 'Foundation' },
    { id: 4, projectId: 1, category: 'Soft Cost', subcategory: 'Legal', description: 'Permitting and Zoning', budgeted: 45000, actual: 48000, vendor: 'Legal Partners LLP', date: '2025-01-10', phase: 'Pre-Construction' }
  ]);

  const [changeOrders, setChangeOrders] = useState([
    { id: 1, projectId: 1, number: 'CO-001', description: 'Additional structural steel', amount: 25000, status: 'Approved', date: '2025-03-20', impact: 'Schedule + 3 days' },
    { id: 2, projectId: 1, number: 'CO-002', description: 'Upgraded HVAC system', amount: 45000, status: 'Pending', date: '2025-04-05', impact: 'Schedule + 5 days' }
  ]);

  const [milestones, setMilestones] = useState([
    { id: 1, projectId: 1, name: 'Permits Approved', targetDate: '2025-01-30', actualDate: '2025-01-28', status: 'Complete' },
    { id: 2, projectId: 1, name: 'Site Work Complete', targetDate: '2025-03-15', actualDate: null, status: 'In Progress' },
    { id: 3, projectId: 1, name: 'Foundation Complete', targetDate: '2025-04-30', actualDate: null, status: 'Not Started' },
    { id: 4, projectId: 1, name: 'Rough-In Complete', targetDate: '2025-07-15', actualDate: null, status: 'Not Started' }
  ]);

  const [selectedProject, setSelectedProject] = useState(1);
  const [expandedSections, setExpandedSections] = useState({
    hardCosts: true,
    softCosts: true,
    changeOrders: true,
    timeline: true
  });

  const hardCostSubcategories = ['Site Work', 'Foundation', 'Structural', 'Envelope', 'MEP', 'Interior Finishes', 'Landscaping', 'Contingency'];
  const softCostSubcategories = ['Architecture', 'Engineering', 'Legal', 'Permitting', 'Insurance', 'Financing', 'Marketing', 'Project Management', 'Testing and Inspection'];

  const currentProject = projects.find(p => p.id === selectedProject);
  const projectCosts = costs.filter(c => c.projectId === selectedProject);
  const projectChangeOrders = changeOrders.filter(co => co.projectId === selectedProject);
  const projectMilestones = milestones.filter(m => m.projectId === selectedProject);

  const hardCosts = projectCosts.filter(c => c.category === 'Hard Cost');
  const softCosts = projectCosts.filter(c => c.category === 'Soft Cost');

  const totalHardBudget = hardCosts.reduce((sum, c) => sum + c.budgeted, 0);
  const totalHardActual = hardCosts.reduce((sum, c) => sum + c.actual, 0);
  const totalSoftBudget = softCosts.reduce((sum, c) => sum + c.budgeted, 0);
  const totalSoftActual = softCosts.reduce((sum, c) => sum + c.actual, 0);
  const totalChangeOrders = projectChangeOrders.reduce((sum, co) => sum + co.amount, 0);
  const approvedChangeOrders = projectChangeOrders.filter(co => co.status === 'Approved').reduce((sum, co) => sum + co.amount, 0);

  const totalBudget = totalHardBudget + totalSoftBudget;
  const totalActual = totalHardActual + totalSoftActual + approvedChangeOrders;
  const variance = totalBudget - totalActual;
  const variancePercent = totalBudget > 0 ? ((variance / totalBudget) * 100).toFixed(1) : '0.0';

  const formatCurrency = (num) => {
    return new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD', minimumFractionDigits: 0 }).format(num);
  };

  const formatDate = (dateString) => {
    if (!dateString) return '-';
    return new Date(dateString).toLocaleDateString('en-US', { month: 'short', day: 'numeric', year: 'numeric' });
  };

  const getStatusColor = (status) => {
    const colors = {
      'Complete': 'bg-green-100 text-green-800',
      'In Progress': 'bg-blue-100 text-blue-800',
      'Not Started': 'bg-gray-100 text-gray-800',
      'Delayed': 'bg-red-100 text-red-800',
      'Approved': 'bg-green-100 text-green-800',
      'Pending': 'bg-yellow-100 text-yellow-800',
      'Rejected': 'bg-red-100 text-red-800',
      'Planning': 'bg-purple-100 text-purple-800'
    };
    return colors[status] || 'bg-gray-100 text-gray-800';
  };

  const toggleSection = (section) => {
    setExpandedSections(prev => ({ ...prev, [section]: !prev[section] }));
  };

  const addProject = () => {
    const newProject = {
      id: projects.length + 1,
      name: 'New Project ' + (projects.length + 1),
      status: 'Planning',
      budget: 0,
      startDate: new Date().toISOString().split('T')[0],
      targetDate: '',
      phase: 'Planning'
    };
    setProjects([...projects, newProject]);
    setSelectedProject(newProject.id);
  };

  const addCost = (category) => {
    const newCost = {
      id: costs.length + 1,
      projectId: selectedProject,
      category: category,
      subcategory: category === 'Hard Cost' ? 'Site Work' : 'Architecture',
      description: '',
      budgeted: 0,
      actual: 0,
      vendor: '',
      date: new Date().toISOString().split('T')[0],
      phase: ''
    };
    setCosts([...costs, newCost]);
  };

  const addChangeOrder = () => {
    const coNumber = projectChangeOrders.length + 1;
    const newCO = {
      id: changeOrders.length + 1,
      projectId: selectedProject,
      number: 'CO-' + String(coNumber).padStart(3, '0'),
      description: '',
      amount: 0,
      status: 'Pending',
      date: new Date().toISOString().split('T')[0],
      impact: ''
    };
    setChangeOrders([...changeOrders, newCO]);
  };

  const addMilestone = () => {
    const newMilestone = {
      id: milestones.length + 1,
      projectId: selectedProject,
      name: '',
      targetDate: '',
      actualDate: null,
      status: 'Not Started'
    };
    setMilestones([...milestones, newMilestone]);
  };

  const updateCost = (id, field, value) => {
    setCosts(costs.map(c => {
      if (c.id === id) {
        const updatedValue = (field === 'budgeted' || field === 'actual') ? Number(value) : value;
        return { ...c, [field]: updatedValue };
      }
      return c;
    }));
  };

  const updateChangeOrder = (id, field, value) => {
    setChangeOrders(changeOrders.map(co => {
      if (co.id === id) {
        const updatedValue = field === 'amount' ? Number(value) : value;
        return { ...co, [field]: updatedValue };
      }
      return co;
    }));
  };

  const updateMilestone = (id, field, value) => {
    setMilestones(milestones.map(m => {
      if (m.id === id) {
        return { ...m, [field]: value };
      }
      return m;
    }));
  };

  const deleteCost = (id) => {
    setCosts(costs.filter(c => c.id !== id));
  };

  const deleteChangeOrder = (id) => {
    setChangeOrders(changeOrders.filter(co => co.id !== id));
  };

  const deleteMilestone = (id) => {
    setMilestones(milestones.filter(m => m.id !== id));
  };

  const renderCostTable = (costsData, subcategories, category) => (
    <div className="overflow-x-auto">
      <table className="w-full">
        <thead>
          <tr className="border-b border-gray-200">
            <th className="text-left py-3 px-2 text-sm font-semibold text-gray-700">Subcategory</th>
            <th className="text-left py-3 px-2 text-sm font-semibold text-gray-700">Description</th>
            <th className="text-left py-3 px-2 text-sm font-semibold text-gray-700">Vendor</th>
            <th className="text-left py-3 px-2 text-sm font-semibold text-gray-700">Phase</th>
            <th className="text-right py-3 px-2 text-sm font-semibold text-gray-700">Budgeted</th>
            <th className="text-right py-3 px-2 text-sm font-semibold text-gray-700">Actual</th>
            <th className="text-right py-3 px-2 text-sm font-semibold text-gray-700">Variance</th>
            <th className="text-center py-3 px-2 text-sm font-semibold text-gray-700">Date</th>
            <th className="w-10"></th>
          </tr>
        </thead>
        <tbody>
          {costsData.map(cost => (
            <tr key={cost.id} className="border-b border-gray-100 hover:bg-gray-50">
              <td className="py-3 px-2">
                <select 
                  value={cost.subcategory}
                  onChange={(e) => updateCost(cost.id, 'subcategory', e.target.value)}
                  className="w-full border border-gray-300 rounded px-2 py-1 text-sm"
                >
                  {subcategories.map(sub => <option key={sub} value={sub}>{sub}</option>)}
                </select>
              </td>
              <td className="py-3 px-2">
                <input 
                  type="text"
                  value={cost.description}
                  onChange={(e) => updateCost(cost.id, 'description', e.target.value)}
                  className="w-full border border-gray-300 rounded px-2 py-1 text-sm"
                  placeholder="Description"
                />
              </td>
              <td className="py-3 px-2">
                <input 
                  type="text"
                  value={cost.vendor}
                  onChange={(e) => updateCost(cost.id, 'vendor', e.target.value)}
                  className="w-full border border-gray-300 rounded px-2 py-1 text-sm"
                  placeholder="Vendor"
                />
              </td>
              <td className="py-3 px-2">
                <input 
                  type="text"
                  value={cost.phase}
                  onChange={(e) => updateCost(cost.id, 'phase', e.target.value)}
                  className="w-full border border-gray-300 rounded px-2 py-1 text-sm"
                  placeholder="Phase"
                />
              </td>
              <td className="py-3 px-2 text-right">
                <input 
                  type="number"
                  value={cost.budgeted}
                  onChange={(e) => updateCost(cost.id, 'budgeted', e.target.value)}
                  className="w-28 border border-gray-300 rounded px-2 py-1 text-sm text-right"
                />
              </td>
              <td className="py-3 px-2 text-right">
                <input 
                  type="number"
                  value={cost.actual}
                  onChange={(e) => updateCost(cost.id, 'actual', e.target.value)}
                  className="w-28 border border-gray-300 rounded px-2 py-1 text-sm text-right"
                />
              </td>
              <td className={'py-3 px-2 text-right text-sm font-medium ' + (cost.budgeted - cost.actual >= 0 ? 'text-green-600' : 'text-red-600')}>
                {formatCurrency(cost.budgeted - cost.actual)}
              </td>
              <td className="py-3 px-2 text-center">
                <input 
                  type="date"
                  value={cost.date}
                  onChange={(e) => updateCost(cost.id, 'date', e.target.value)}
                  className="border border-gray-300 rounded px-2 py-1 text-sm"
                />
              </td>
              <td className="py-3 px-2 text-center">
                <button onClick={() => deleteCost(cost.id)} className="text-red-500 hover:text-red-700">
                  <X className="w-4 h-4" />
                </button>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );

  return (
    <div className="min-h-screen bg-gray-50 p-6">
      <div className="max-w-7xl mx-auto">
        <div className="bg-white rounded-lg shadow-sm p-6 mb-6">
          <div className="flex justify-between items-start mb-4">
            <div>
              <h1 className="text-3xl font-bold text-gray-900">Development Project Tracker</h1>
              <p className="text-gray-600 mt-1">Comprehensive cost and timeline management</p>
            </div>
            <button onClick={addProject} className="flex items-center gap-2 bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition-colors">
              <Plus className="w-4 h-4" />
              New Project
            </button>
          </div>

          <div className="flex items-center gap-4">
            <label className="text-sm font-medium text-gray-700">Active Project:</label>
            <select 
              value={selectedProject}
              onChange={(e) => setSelectedProject(Number(e.target.value))}
              className="flex-1 max-w-md border border-gray-300 rounded-lg px-4 py-2 focus:ring-2 focus:ring-blue-500 focus:border-transparent"
            >
              {projects.map(p => (
                <option key={p.id} value={p.id}>{p.name}</option>
              ))}
            </select>
            <span className={'px-3 py-1 rounded-full text-sm font-medium ' + getStatusColor(currentProject?.status)}>
              {currentProject?.status}
            </span>
          </div>
        </div>

        <div className="bg-white rounded-lg shadow-sm mb-6">
          <div className="border-b border-gray-200">
            <div className="flex gap-1 p-2">
              {['dashboard', 'costs', 'change-orders', 'timeline'].map(tab => (
                <button
                  key={tab}
                  onClick={() => setActiveTab(tab)}
                  className={'px-6 py-3 rounded-lg font-medium transition-colors ' + (activeTab === tab ? 'bg-blue-50 text-blue-700' : 'text-gray-600 hover:bg-gray-50')}
                >
                  {tab.split('-').map(w => w.charAt(0).toUpperCase() + w.slice(1)).join(' ')}
                </button>
              ))}
            </div>
          </div>
        </div>

        {activeTab === 'dashboard' && (
          <div className="space-y-6">
            <div className="grid grid-cols-1 md:grid-cols-4 gap-6">
              <div className="bg-white rounded-lg shadow-sm p-6">
                <div className="text-sm text-gray-600 mb-1">Total Budget</div>
                <div className="text-2xl font-bold text-gray-900">{formatCurrency(totalBudget)}</div>
                <div className="text-xs text-gray-500 mt-1">Original allocation</div>
              </div>
              <div className="bg-white rounded-lg shadow-sm p-6">
                <div className="text-sm text-gray-600 mb-1">Total Spent</div>
                <div className="text-2xl font-bold text-gray-900">{formatCurrency(totalActual)}</div>
                <div className="text-xs text-gray-500 mt-1">Including approved COs</div>
              </div>
              <div className="bg-white rounded-lg shadow-sm p-6">
                <div className="text-sm text-gray-600 mb-1">Variance</div>
                <div className={'text-2xl font-bold ' + (variance >= 0 ? 'text-green-600' : 'text-red-600')}>
                  {formatCurrency(Math.abs(variance))}
                </div>
                <div className="text-xs text-gray-500 mt-1">{variance >= 0 ? 'Under' : 'Over'} budget ({variancePercent}%)</div>
              </div>
              <div className="bg-white rounded-lg shadow-sm p-6">
                <div className="text-sm text-gray-600 mb-1">Change Orders</div>
                <div className="text-2xl font-bold text-gray-900">{formatCurrency(totalChangeOrders)}</div>
                <div className="text-xs text-gray-500 mt-1">{projectChangeOrders.length} total orders</div>
              </div>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div className="bg-white rounded-lg shadow-sm p-6">
                <h3 className="text-lg font-semibold text-gray-900 mb-4">Hard Costs</h3>
                <div className="space-y-3">
                  <div className="flex justify-between items-center">
                    <span className="text-gray-600">Budgeted:</span>
                    <span className="font-semibold">{formatCurrency(totalHardBudget)}</span>
                  </div>
                  <div className="flex justify-between items-center">
                    <span className="text-gray-600">Actual:</span>
                    <span className="font-semibold">{formatCurrency(totalHardActual)}</span>
                  </div>
                  <div className="flex justify-between items-center pt-2 border-t">
                    <span className="text-gray-900 font-medium">Variance:</span>
                    <span className={'font-semibold ' + (totalHardBudget - totalHardActual >= 0 ? 'text-green-600' : 'text-red-600')}>
                      {formatCurrency(Math.abs(totalHardBudget - totalHardActual))}
                    </span>
                  </div>
                  <div className="w-full bg-gray-200 rounded-full h-3 mt-3">
                    <div 
                      className={'h-3 rounded-full ' + (totalHardActual / totalHardBudget > 1 ? 'bg-red-500' : 'bg-blue-500')}
                      style={{ width: Math.min((totalHardActual / totalHardBudget) * 100, 100) + '%' }}
                    ></div>
                  </div>
                  <div className="text-xs text-gray-500 text-right">{totalHardBudget > 0 ? ((totalHardActual / totalHardBudget) * 100).toFixed(1) : '0.0'}% spent</div>
                </div>
              </div>

              <div className="bg-white rounded-lg shadow-sm p-6">
                <h3 className="text-lg font-semibold text-gray-900 mb-4">Soft Costs</h3>
                <div className="space-y-3">
                  <div className="flex justify-between items-center">
                    <span className="text-gray-600">Budgeted:</span>
                    <span className="font-semibold">{formatCurrency(totalSoftBudget)}</span>
                  </div>
                  <div className="flex justify-between items-center">
                    <span className="text-gray-600">Actual:</span>
                    <span className="font-semibold">{formatCurrency(totalSoftActual)}</span>
                  </div>
                  <div className="flex justify-between items-center pt-2 border-t">
                    <span className="text-gray-900 font-medium">Variance:</span>
                    <span className={'font-semibold ' + (totalSoftBudget - totalSoftActual >= 0 ? 'text-green-600' : 'text-red-600')}>
                      {formatCurrency(Math.abs(totalSoftBudget - totalSoftActual))}
                    </span>
                  </div>
                  <div className="w-full bg-gray-200 rounded-full h-3 mt-3">
                    <div 
                      className={'h-3 rounded-full ' + (totalSoftActual / totalSoftBudget > 1 ? 'bg-red-500' : 'bg-green-500')}
                      style={{ width: Math.min((totalSoftActual / totalSoftBudget) * 100, 100) + '%' }}
                    ></div>
                  </div>
                  <div className="text-xs text-gray-500 text-right">{totalSoftBudget > 0 ? ((totalSoftActual / totalSoftBudget) * 100).toFixed(1) : '0.0'}% spent</div>
                </div>
              </div>
            </div>

            <div className="bg-white rounded-lg shadow-sm p-6">
              <h3 className="text-lg font-semibold text-gray-900 mb-4">Recent Change Orders</h3>
              <div className="space-y-3">
                {projectChangeOrders.slice(0, 5).map(co => (
                  <div key={co.id} className="flex justify-between items-center p-3 bg-gray-50 rounded-lg">
                    <div>
                      <div className="font-medium text-gray-900">{co.number}</div>
                      <div className="text-sm text-gray-600">{co.description}</div>
                    </div>
                    <div className="text-right">
                      <div className="font-semibold text-gray-900">{formatCurrency(co.amount)}</div>
                      <span className={'text-xs px-2 py-1 rounded-full ' + getStatusColor(co.status)}>
                        {co.status}
                      </span>
                    </div>
                  </div>
                ))}
              </div>
            </div>

            <div className="bg-white rounded-lg shadow-sm p-6">
              <h3 className="text-lg font-semibold text-gray-900 mb-4">Upcoming Milestones</h3>
              <div className="space-y-3">
                {projectMilestones.filter(m => m.status !== 'Complete').slice(0, 4).map(m => (
                  <div key={m.id} className="flex justify-between items-center p-3 bg-gray-50 rounded-lg">
                    <div>
                      <div className="font-medium text-gray-900">{m.name}</div>
                      <div className="text-sm text-gray-600">Target: {formatDate(m.targetDate)}</div>
                    </div>
                    <span className={'px-3 py-1 rounded-full text-xs font-medium ' + getStatusColor(m.status)}>
                      {m.status}
                    </span>
                  </div>
                ))}
              </div>
            </div>
          </div>
        )}

        {activeTab === 'costs' && (
          <div className="space-y-6">
            <div className="bg-white rounded-lg shadow-sm">
              <div className="p-6 border-b border-gray-200">
                <div className="flex justify-between items-center">
                  <div className="flex items-center gap-3">
                    <button onClick={() => toggleSection('hardCosts')} className="text-gray-600 hover:text-gray-900">
                      {expandedSections.hardCosts ? <ChevronUp className="w-5 h-5" /> : <ChevronDown className="w-5 h-5" />}
                    </button>
                    <h3 className="text-xl font-semibold text-gray-900">Hard Costs</h3>
                    <span className="text-sm text-gray-500">({hardCosts.length} items)</span>
                  </div>
                  <div className="text-right">
                    <div className="text-sm text-gray-600">Budget: {formatCurrency(totalHardBudget)}</div>
                    <div className="text-sm font-semibold text-gray-900">Actual: {formatCurrency(totalHardActual)}</div>
                  </div>
                </div>
              </div>
              {expandedSections.hardCosts && (
                <div className="p-6">
                  {renderCostTable(hardCosts, hardCostSubcategories, 'Hard Cost')}
                  <button onClick={() => addCost('Hard Cost')} className="mt-4 flex items-center gap-2 text-blue-600 hover:text-blue-700 font-medium text-sm">
                    <Plus className="w-4 h-4" />
                    Add Hard Cost Line Item
                  </button>
                </div>
              )}
            </div>

            <div className="bg-white rounded-lg shadow-sm">
              <div className="p-6 border-b border-gray-200">
                <div className="flex justify-between items-center">
                  <div className="flex items-center gap-3">
                    <button onClick={() => toggleSection('softCosts')} className="text-gray-600 hover:text-gray-900">
                      {expandedSections.softCosts ? <ChevronUp className="w-5 h-5" /> : <ChevronDown className="w-5 h-5" />}
                    </button>
                    <h3 className="text-xl font-semibold text-gray-900">Soft Costs</h3>
                    <span className="text-sm text-gray-500">({softCosts.length} items)</span>
                  </div>
                  <div className="text-right">
                    <div className="text-sm text-gray-600">Budget: {formatCurrency(totalSoftBudget)}</div>
                    <div className="text-sm font-semibold text-gray-900">Actual: {formatCurrency(totalSoftActual)}</div>
                  </div>
                </div>
              </div>
              {expandedSections.softCosts && (
                <div className="p-6">
                  {renderCostTable(softCosts, softCostSubcategories, 'Soft Cost')}
                  <button onClick={() => addCost('Soft Cost')} className="mt-4 flex items-center gap-2 text-blue-600 hover:text-blue-700 font-medium text-sm">
                    <Plus className="w-4 h-4" />
                    Add Soft Cost Line Item
                  </button>
                </div>
              )}
            </div>
          </div>
        )}

        {activeTab === 'change-orders' && (
          <div className="bg-white rounded-lg shadow-sm">
            <div className="p-6 border-b border-gray-200">
              <div className="flex justify-between items-center">
                <div>
                  <h3 className="text-xl font-semibold text-gray-900">Change Orders</h3>
                  <p className="text-sm text-gray-600 mt-1">Total: {formatCurrency(totalChangeOrders)} ({projectChangeOrders.length} orders)</p>
                </div>
                <button onClick={addChangeOrder} className="flex items-center gap-2 bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition-colors">
                  <Plus className="w-4 h-4" />
                  New Change Order
                </button>
              </div>
            </div>
            <div className="p-6">
              <div className="overflow-x-auto">
                <table className="w-full">
                  <thead>
                    <tr className="border-b border-gray-200">
                      <th className="text-left py-3 px-2 text-sm font-semibold text-gray-700">CO Number</th>
                      <th className="text-left py-3 px-2 text-sm font-semibold text-gray-700">Description</th>
                      <th className="text-right py-3 px-2 text-sm font-semibold text-gray-700">Amount</th>
                      <th className="text-center py-3 px-2 text-sm font-semibold text-gray-700">Status</th>
                      <th className="text-center py-3 px-2 text-sm font-semibold text-gray-700">Date</th>
                      <th className="text-left py-3 px-2 text-sm font-semibold text-gray-700">Schedule Impact</th>
                      <th className="w-10"></th>
                    </tr>
                  </thead>
                  <tbody>
                    {projectChangeOrders.map(co => (
                      <tr key={co.id} className="border-b border-gray-100 hover:bg-gray-50">
                        <td className="py-3 px-2">
                          <input 
                            type="text"
                            value={co.number}
                            onChange={(e) => updateChangeOrder(co.id, 'number', e.target.value)}
                            className="w-28 border border-gray-300 rounded px-2 py-1 text-sm font-medium"
                          />
                        </td>
                        <td className="py-3 px-2">
                          <input 
                            type="text"
                            value={co.description}
                            onChange={(e) => updateChangeOrder(co.id, 'description', e.target.value)}
                            className="w-full border border-gray-300 rounded px-2 py-1 text-sm"
                            placeholder="Change order description"
                          />
                        </td>
                        <td className="py-3 px-2 text-right">
                          <input 
                            type="number"
                            value={co.amount}
                            onChange={(e) => updateChangeOrder(co.id, 'amount', e.target.value)}
                            className="w-32 border border-gray-300 rounded px-2 py-1 text-sm text-right font-medium"
                          />
                        </td>
                        <td className="py-3 px-2 text-center">
                          <select 
                            value={co.status}
                            onChange={(e) => updateChangeOrder(co.id, 'status', e.target.value)}
                            className={'border border-gray-300 rounded px-3 py-1 text-sm font-medium ' + getStatusColor(co.status)}
                          >
                            <option value="Pending">Pending</option>
                            <option value="Approved">Approved</option>
                            <option value="Rejected">Rejected</option>
                          </select>
                        </td>
                        <td className="py-3 px-2 text-center">
                          <input 
                            type="date"
                            value={co.date}
                            onChange={(e) => updateChangeOrder(co.id, 'date', e.target.value)}
                            className="border border-gray-300 rounded px-2 py-1 text-sm"
                          />
                        </td>
                        <td className="py-3 px-2">
                          <input 
                            type="text"
                            value={co.impact}
                            onChange={(e) => updateChangeOrder(co.id, 'impact', e.target.value)}
                            className="w-full border border-gray-300 rounded px-2 py-1 text-sm"
                            placeholder="e.g., Schedule + 5 days"
                          />
                        </td>
                        <td className="py-3 px-2 text-center">
                          <button onClick={() => deleteChangeOrder(co.id)} className="text-red-500 hover:text-red-700">
                            <X className="w-4 h-4" />
                          </button>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
              
              <div className="mt-6 grid grid-cols-3 gap-4">
                <div className="bg-yellow-50 rounded-lg p-4">
                  <div className="text-sm text-yellow-800 font-medium">Pending</div>
                  <div className="text-2xl font-bold text-yellow-900 mt-1">
                    {formatCurrency(projectChangeOrders.filter(co => co.status === 'Pending').reduce((sum, co) => sum + co.amount, 0))}
                  </div>
                  <div className="text-xs text-yellow-700 mt-1">
                    {projectChangeOrders.filter(co => co.status === 'Pending').length} orders
                  </div>
                </div>
                <div className="bg-green-50 rounded-lg p-4">
                  <div className="text-sm text-green-800 font-medium">Approved</div>
                  <div className="text-2xl font-bold text-green-900 mt-1">
                    {formatCurrency(approvedChangeOrders)}
                  </div>
                  <div className="text-xs text-green-700 mt-1">
                    {projectChangeOrders.filter(co => co.status === 'Approved').length} orders
                  </div>
                </div>
                <div className="bg-red-50 rounded-lg p-4">
                  <div className="text-sm text-red-800 font-medium">Rejected</div>
                  <div className="text-2xl font-bold text-red-900 mt-1">
                    {formatCurrency(projectChangeOrders.filter(co => co.status === 'Rejected').reduce((sum, co) => sum + co.amount, 0))}
                  </div>
                  <div className="text-xs text-red-700 mt-1">
                    {projectChangeOrders.filter(co => co.status === 'Rejected').length} orders
                  </div>
                </div>
              </div>
            </div>
          </div>
        )}

        {activeTab === 'timeline' && (
          <div className="bg-white rounded-lg shadow-sm">
            <div className="p-6 border-b border-gray-200">
              <div className="flex justify-between items-center">
                <div>
                  <h3 className="text-xl font-semibold text-gray-900">Project Timeline and Milestones</h3>
                  <p className="text-sm text-gray-600 mt-1">
                    {currentProject?.startDate && 'Started: ' + formatDate(currentProject.startDate)}
                    {currentProject?.targetDate && ' • Target: ' + formatDate(currentProject.targetDate)}
                  </p>
                </div>
                <button onClick={addMilestone} className="flex items-center gap-2 bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition-colors">
                  <Plus className="w-4 h-4" />
                  Add Milestone
                </button>
              </div>
            </div>
            <div className="p-6">
              <div className="overflow-x-auto">
                <table className="w-full">
                  <thead>
                    <tr className="border-b border-gray-200">
                      <th className="text-left py-3 px-2 text-sm font-semibold text-gray-700">Milestone</th>
                      <th className="text-center py-3 px-2 text-sm font-semibold text-gray-700">Target Date</th>
                      <th className="text-center py-3 px-2 text-sm font-semibold text-gray-700">Actual Date</th>
                      <th className="text-center py-3 px-2 text-sm font-semibold text-gray-700">Status</th>
                      <th className="text-center py-3 px-2 text-sm font-semibold text-gray-700">Variance</th>
                      <th className="w-10"></th>
                    </tr>
                  </thead>
                  <tbody>
                    {projectMilestones.map(milestone => {
                      const variance = milestone.actualDate && milestone.targetDate 
                        ? Math.round((new Date(milestone.actualDate) - new Date(milestone.targetDate)) / (1000 * 60 * 60 * 24))
                        : null;
                      
                      return (
                        <tr key={milestone.id} className="border-b border-gray-100 hover:bg-gray-50">
                          <td className="py-3 px-2">
                            <input 
                              type="text"
                              value={milestone.name}
                              onChange={(e) => updateMilestone(milestone.id, 'name', e.target.value)}
                              className="w-full border border-gray-300 rounded px-2 py-1 text-sm font-medium"
                              placeholder="Milestone name"
                            />
                          </td>
                          <td className="py-3 px-2 text-center">
                            <input 
                              type="date"
                              value={milestone.targetDate}
                              onChange={(e) => updateMilestone(milestone.id, 'targetDate', e.target.value)}
                              className="border border-gray-300 rounded px-2 py-1 text-sm"
                            />
                          </td>
                          <td className="py-3 px-2 text-center">
                            <input 
                              type="date"
                              value={milestone.actualDate || ''}
                              onChange={(e) => updateMilestone(milestone.id, 'actualDate', e.target.value)}
                              className="border border-gray-300 rounded px-2 py-1 text-sm"
                            />
                          </td>
                          <td className="py-3 px-2 text-center">
                            <select 
                              value={milestone.status}
                              onChange={(e) => updateMilestone(milestone.id, 'status', e.target.value)}
                              className={'border border-gray-300 rounded px-3 py-1 text-sm font-medium ' + getStatusColor(milestone.status)}
                            >
                              <option value="Not Started">Not Started</option>
                              <option value="In Progress">In Progress</option>
                              <option value="Complete">Complete</option>
                              <option value="Delayed">Delayed</option>
                            </select>
                          </td>
                          <td className="py-3 px-2 text-center">
                            {variance !== null && (
                              <span className={'text-sm font-medium ' + (variance <= 0 ? 'text-green-600' : 'text-red-600')}>
                                {variance > 0 ? '+' : ''}{variance} days
                              </span>
                            )}
                          </td>
                          <td className="py-3 px-2 text-center">
                            <button onClick={() => deleteMilestone(milestone.id)} className="text-red-500 hover:text-red-700">
                              <X className="w-4 h-4" />
                            </button>
                          </td>
                        </tr>
                      );
                    })}
                  </tbody>
                </table>
              </div>

              <div className="mt-8">
                <h4 className="text-lg font-semibold text-gray-900 mb-4">Timeline Overview</h4>
                <div className="space-y-3">
                  {projectMilestones.map((milestone, index) => {
                    return (
                      <div key={milestone.id} className="relative">
                        <div className="flex items-center gap-4">
                          <div className={'w-8 h-8 rounded-full flex items-center justify-center text-white text-sm font-bold ' + (
                            milestone.status === 'Complete' ? 'bg-green-500' :
                            milestone.status === 'In Progress' ? 'bg-blue-500' :
                            milestone.status === 'Delayed' ? 'bg-red-500' :
                            'bg-gray-400'
                          )}>
                            {index + 1}
                          </div>
                          <div className="flex-1">
                            <div className="flex justify-between items-center mb-1">
                              <span className="font-medium text-gray-900">{milestone.name}</span>
                              <span className="text-sm text-gray-600">{formatDate(milestone.targetDate)}</span>
                            </div>
                            <div className="w-full bg-gray-200 rounded-full h-2">
                              <div 
                                className={'h-2 rounded-full ' + (
                                  milestone.status === 'Complete' ? 'bg-green-500' :
                                  milestone.status === 'In Progress' ? 'bg-blue-500' :
                                  milestone.status === 'Delayed' ? 'bg-red-500' :
                                  'bg-gray-400'
                                )}
                                style={{ width: milestone.status === 'Complete' ? '100%' : milestone.status === 'In Progress' ? '50%' : '0%' }}
                              ></div>
                            </div>
                          </div>
                        </div>
                        {index < projectMilestones.length - 1 && (
                          <div className="absolute left-4 top-10 w-0.5 h-6 bg-gray-300"></div>
                        )}
                      </div>
                    );
                  })}
                </div>
              </div>
            </div>
          </div>
        )}
      </div>
    </div>
  );
};

export default ProjectManagerTracker;
