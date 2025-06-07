import React, { useState, useEffect } from 'react';
import { createClient } from '@supabase/supabase-js';
import { 
  AlertTriangle, 
  FileText, 
  Euro, 
  TrendingUp, 
  Star, 
  BarChart3,
  Plus,
  Search,
  Filter,
  Download,
  Upload,
  Clock,
  CheckCircle,
  XCircle,
  AlertCircle,
  Calendar,
  Users,
  Package,
  Mail,
  Save,
  Edit,
  Trash2,
  Eye
} from 'lucide-react';

// Configuration Supabase
const supabaseUrl = import.meta.env.VITE_SUPABASE_URL;
const supabaseAnonKey = import.meta.env.VITE_SUPABASE_ANON_KEY;

let supabase = null;
if (supabaseUrl && supabaseAnonKey) {
  supabase = createClient(supabaseUrl, supabaseAnonKey);
}

const AutoClaimsSystem = () => {
  const [activeModule, setActiveModule] = useState('dashboard');
  const [claims, setClaims] = useState([]);
  const [suppliers, setSuppliers] = useState([]);
  const [costs, setCosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [showNewClaimForm, setShowNewClaimForm] = useState(false);

  // Données par défaut si Supabase n'est pas configuré
  const defaultClaims = [
    {
      id: 'demo-001',
      reclamation_nr: 'RK-2024-001',
      supplier_name: 'BOSCH GmbH',
      part_number: 'BP-45789',
      description: 'Défaut étanchéité joint torique',
      severity: 'KRITISCH',
      date_sent: '2024-06-01',
      due_8d: '2024-06-08',
      received_8d: null,
      supplier_status: 'En cours',
      damages_eur: 15420,
      recovery_status: 'Pending',
      responsible: 'M. Schmidt',
      escalation_level: 'Level 2'
    },
    {
      id: 'demo-002',
      reclamation_nr: 'RK-2024-002',
      supplier_name: 'Continental AG',
      part_number: 'CT-98765',
      description: 'Capteur défaillant après 100h',
      severity: 'MAJOR',
      date_sent: '2024-06-03',
      due_8d: '2024-06-10',
      received_8d: '2024-06-09',
      supplier_status: 'Résolu',
      damages_eur: 8750,
      recovery_status: 'Completed',
      responsible: 'Mme Weber',
      escalation_level: 'Level 1'
    }
  ];

  const defaultSuppliers = [
    { id: 1, name: 'BOSCH GmbH', rating: 'B', claims: 3, on_time_8d_rate: 67, total_cost: 45000, recovered: 15000 },
    { id: 2, name: 'Continental AG', rating: 'A', claims: 1, on_time_8d_rate: 100, total_cost: 8750, recovered: 8750 },
    { id: 3, name: 'ZF Group', rating: 'A', claims: 2, on_time_8d_rate: 100, total_cost: 12000, recovered: 12000 }
  ];

  const defaultCosts = [
    { id: 1, reclamation_id: 'demo-001', category: 'Production Loss', amount_eur: 12000, recovered_eur: 0 },
    { id: 2, reclamation_id: 'demo-001', category: 'Inspection Cost', amount_eur: 2420, recovered_eur: 0 },
    { id: 3, reclamation_id: 'demo-002', category: 'Production Loss', amount_eur: 6000, recovered_eur: 6000 },
    { id: 4, reclamation_id: 'demo-002', category: 'Testing Cost', amount_eur: 2750, recovered_eur: 2750 }
  ];

  useEffect(() => {
    loadData();
  }, []);

  const loadData = async () => {
    setLoading(true);
    try {
      if (supabase) {
        // Charger depuis Supabase
        const [claimsResult, suppliersResult, costsResult] = await Promise.all([
          supabase.from('reclamations').select(`
            *,
            suppliers(name, email, contact_person)
          `).order('created_at', { ascending: false }),
          supabase.from('suppliers').select('*'),
          supabase.from('costs').select('*')
        ]);

        if (claimsResult.error) throw claimsResult.error;
        if (suppliersResult.error) throw suppliersResult.error;
        if (costsResult.error) throw costsResult.error;

        setClaims(claimsResult.data.map(claim => ({
          ...claim,
          supplier_name: claim.suppliers?.name || 'N/A'
        })));
        setSuppliers(suppliersResult.data);
        setCosts(costsResult.data);
      } else {
        // Utiliser les données par défaut
        setClaims(defaultClaims);
        setSuppliers(defaultSuppliers);
        setCosts(defaultCosts);
      }
    } catch (error) {
      console.error('Erreur lors du chargement:', error);
      // Fallback vers les données par défaut
      setClaims(defaultClaims);
      setSuppliers(defaultSuppliers);
      setCosts(defaultCosts);
    }
    setLoading(false);
  };

  const createClaim = async (claimData) => {
    try {
      if (supabase) {
        const { data, error } = await supabase
          .from('reclamations')
          .insert([claimData])
          .select(`
            *,
            suppliers(name, email, contact_person)
          `)
          .single();

        if (error) throw error;

        setClaims(prev => [{
          ...data,
          supplier_name: data.suppliers?.name || 'N/A'
        }, ...prev]);
        
        return data;
      } else {
        // Mode démonstration
        const newClaim = {
          ...claimData,
          id: 'demo-' + Date.now(),
          reclamation_nr: `RK-2024-${String(claims.length + 1).padStart(3, '0')}`,
          created_at: new Date().toISOString()
        };
        setClaims(prev => [newClaim, ...prev]);
        return newClaim;
      }
    } catch (error) {
      console.error('Erreur lors de la création:', error);
      throw error;
    }
  };

  const modules = [
    { id: 'dashboard', name: 'Dashboard', icon: BarChart3, color: 'bg-blue-500' },
    { id: 'claims', name: 'Réclamations', icon: AlertTriangle, color: 'bg-red-500' },
    { id: 'costs', name: 'Coûts', icon: Euro, color: 'bg-green-500' },
    { id: '8d', name: 'Suivi 8D', icon: FileText, color: 'bg-purple-500' },
    { id: 'suppliers', name: 'Fournisseurs', icon: Star, color: 'bg-orange-500' }
  ];

  const getSeverityColor = (severity) => {
    switch(severity) {
      case 'KRITISCH': return 'bg-red-100 text-red-800 border-red-200';
      case 'MAJOR': return 'bg-orange-100 text-orange-800 border-orange-200';
      case 'MINOR': return 'bg-yellow-100 text-yellow-800 border-yellow-200';
      default: return 'bg-gray-100 text-gray-800 border-gray-200';
    }
  };

  const getStatusIcon = (status) => {
    switch(status) {
      case 'Résolu': return <CheckCircle className="w-4 h-4 text-green-500" />;
      case 'En cours': return <Clock className="w-4 h-4 text-orange-500" />;
      case 'Retard': return <XCircle className="w-4 h-4 text-red-500" />;
      default: return <AlertCircle className="w-4 h-4 text-gray-500" />;
    }
  };

  const NewClaimForm = ({ onSubmit, onCancel }) => {
    const [formData, setFormData] = useState({
      part_number: '',
      description: '',
      severity: 'MAJOR',
      supplier_id: '',
      date_sent: new Date().toISOString().split('T')[0],
      damages_eur: 0,
      responsible: '',
      delivered_qty: 0,
      defective_qty: 0,
      serial_number: '',
      customer_impacted: '',
      production_line: ''
    });

    const handleSubmit = async () => {
      try {
        await onSubmit(formData);
        setShowNewClaimForm(false);
        alert('Réclamation créée avec succès !');
      } catch (error) {
        alert('Erreur lors de la création: ' + error.message);
      }
    };

    return (
      <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div className="bg-white rounded-xl p-6 w-full max-w-2xl max-h-[90vh] overflow-y-auto">
          <h3 className="text-xl font-bold mb-4">Nouvelle Réclamation</h3>
          
          <div className="space-y-4">
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div>
                <label className="block text-sm font-medium mb-1">Numéro de Pièce</label>
                <input
                  type="text"
                  value={formData.part_number}
                  onChange={(e) => setFormData({...formData, part_number: e.target.value})}
                  className="w-full px-3 py-2 border border-gray-300 rounded-lg"
                  required
                />
              </div>
              
              <div>
                <label className="block text-sm font-medium mb-1">Sévérité</label>
                <select
                  value={formData.severity}
                  onChange={(e) => setFormData({...formData, severity: e.target.value})}
                  className="w-full px-3 py-2 border border-gray-300 rounded-lg"
                >
                  <option value="MINOR">Minor</option>
                  <option value="MAJOR">Major</option>
                  <option value="KRITISCH">Kritisch</option>
                </select>
              </div>
            </div>

            <div>
              <label className="block text-sm font-medium mb-1">Description du Problème</label>
              <textarea
                value={formData.description}
                onChange={(e) => setFormData({...formData, description: e.target.value})}
                className="w-full px-3 py-2 border border-gray-300 rounded-lg"
                rows="3"
                required
              />
            </div>

            <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
              <div>
                <label className="block text-sm font-medium mb-1">Date d'Envoi</label>
                <input
                  type="date"
                  value={formData.date_sent}
                  onChange={(e) => setFormData({...formData, date_sent: e.target.value})}
                  className="w-full px-3 py-2 border border-gray-300 rounded-lg"
                  required
                />
              </div>
              
              <div>
                <label className="block text-sm font-medium mb-1">Dommages (€)</label>
                <input
                  type="number"
                  value={formData.damages_eur}
                  onChange={(e) => setFormData({...formData, damages_eur: parseFloat(e.target.value) || 0})}
                  className="w-full px-3 py-2 border border-gray-300 rounded-lg"
                />
              </div>
              
              <div>
                <label className="block text-sm font-medium mb-1">Responsable</label>
                <input
                  type="text"
                  value={formData.responsible}
                  onChange={(e) => setFormData({...formData, responsible: e.target.value})}
                  className="w-full px-3 py-2 border border-gray-300 rounded-lg"
                />
              </div>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div>
                <label className="block text-sm font-medium mb-1">Quantité Livrée</label>
                <input
                  type="number"
                  value={formData.delivered_qty}
                  onChange={(e) => setFormData({...formData, delivered_qty: parseInt(e.target.value) || 0})}
                  className="w-full px-3 py-2 border border-gray-300 rounded-lg"
                />
              </div>
              
              <div>
                <label className="block text-sm font-medium mb-1">Quantité Défectueuse</label>
                <input
                  type="number"
                  value={formData.defective_qty}
                  onChange={(e) => setFormData({...formData, defective_qty: parseInt(e.target.value) || 0})}
                  className="w-full px-3 py-2 border border-gray-300 rounded-lg"
                />
              </div>
            </div>

            <div className="flex gap-3 pt-4">
              <button
                onClick={handleSubmit}
                className="flex-1 bg-red-500 text-white py-2 px-4 rounded-lg hover:bg-red-600 flex items-center justify-center gap-2"
              >
                <Save className="w-4 h-4" />
                Créer la Réclamation
              </button>
              <button
                onClick={() => setShowNewClaimForm(false)}
                className="px-6 py-2 border border-gray-300 rounded-lg hover:bg-gray-50"
              >
                Annuler
              </button>
            </div>
          </div>
        </div>
      </div>
    );
  };

  const DashboardModule = () => {
    const totalClaims = claims.length;
    const criticalClaims = claims.filter(c => c.severity === 'KRITISCH').length;
    const totalDamages = claims.reduce((sum, claim) => sum + (claim.damages_eur || 0), 0);
    const totalRecovered = costs.reduce((sum, cost) => sum + (cost.recovered_eur || 0), 0);
    const avgResolutionTime = 5.2;

    return (
      <div className="space-y-6">
        <div className="flex justify-between items-center">
          <h2 className="text-3xl font-bold text-gray-800">Dashboard Réclamations</h2>
          <div className="text-sm text-gray-500">
            {supabase ? 'Données en temps réel' : 'Mode démonstration'}
          </div>
        </div>

        {!supabase && (
          <div className="bg-blue-50 border border-blue-200 rounded-lg p-4">
            <div className="flex items-center gap-2">
              <AlertCircle className="w-5 h-5 text-blue-600" />
              <span className="text-blue-800 font-medium">Mode Démonstration</span>
            </div>
            <p className="text-blue-700 mt-1">
              Configurez Supabase pour utiliser des données réelles. Voir le guide ci-dessus pour les instructions.
            </p>
          </div>
        )}

        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm font-medium text-gray-600">Total Réclamations</p>
                <p className="text-3xl font-bold text-gray-900">{totalClaims}</p>
              </div>
              <div className="w-12 h-12 bg-blue-100 rounded-lg flex items-center justify-center">
                <AlertTriangle className="w-6 h-6 text-blue-600" />
              </div>
            </div>
            <div className="mt-4 flex items-center text-sm">
              <TrendingUp className="w-4 h-4 text-green-500 mr-1" />
              <span className="text-green-600">+2.5%</span>
              <span className="text-gray-500 ml-1">vs mois dernier</span>
            </div>
          </div>

          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm font-medium text-gray-600">Critiques</p>
                <p className="text-3xl font-bold text-red-600">{criticalClaims}</p>
              </div>
              <div className="w-12 h-12 bg-red-100 rounded-lg flex items-center justify-center">
                <XCircle className="w-6 h-6 text-red-600" />
              </div>
            </div>
            <div className="mt-4 flex items-center text-sm">
              <span className="text-red-600">Action urgente</span>
            </div>
          </div>

          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm font-medium text-gray-600">Dommages Totaux</p>
                <p className="text-3xl font-bold text-gray-900">{totalDamages.toLocaleString()}€</p>
              </div>
              <div className="w-12 h-12 bg-yellow-100 rounded-lg flex items-center justify-center">
                <Euro className="w-6 h-6 text-yellow-600" />
              </div>
            </div>
            <div className="mt-4 flex items-center text-sm">
              <span className="text-green-600">
                Recovery: {totalDamages > 0 ? ((totalRecovered/totalDamages)*100).toFixed(1) : 0}%
              </span>
            </div>
          </div>

          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm font-medium text-gray-600">Résolution Moy.</p>
                <p className="text-3xl font-bold text-gray-900">{avgResolutionTime}j</p>
              </div>
              <div className="w-12 h-12 bg-green-100 rounded-lg flex items-center justify-center">
                <Clock className="w-6 h-6 text-green-600" />
              </div>
            </div>
            <div className="mt-4 flex items-center text-sm">
              <span className="text-green-600">-1.2j</span>
              <span className="text-gray-500 ml-1">vs objectif</span>
            </div>
          </div>
        </div>

        <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
            <h3 className="text-lg font-semibold text-gray-800 mb-4">Réclamations par Sévérité</h3>
            <div className="space-y-3">
              <div className="flex justify-between items-center">
                <span className="text-gray-600">Critiques</span>
                <div className="flex items-center">
                  <div className="w-32 bg-gray-200 rounded-full h-2 mr-3">
                    <div className="bg-red-500 h-2 rounded-full" style={{width: `${(criticalClaims/totalClaims)*100}%`}}></div>
                  </div>
                  <span className="text-sm font-medium">{criticalClaims}</span>
                </div>
              </div>
              <div className="flex justify-between items-center">
                <span className="text-gray-600">Majeures</span>
                <div className="flex items-center">
                  <div className="w-32 bg-gray-200 rounded-full h-2 mr-3">
                    <div className="bg-orange-500 h-2 rounded-full" style={{width: `${((totalClaims-criticalClaims)/totalClaims)*100}%`}}></div>
                  </div>
                  <span className="text-sm font-medium">{totalClaims - criticalClaims}</span>
                </div>
              </div>
            </div>
          </div>

          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
            <h3 className="text-lg font-semibold text-gray-800 mb-4">Top Fournisseurs</h3>
            <div className="space-y-3">
              {suppliers.slice(0, 3).map((supplier, index) => (
                <div key={index} className="flex justify-between items-center p-3 bg-gray-50 rounded-lg">
                  <div className="flex items-center">
                    <div className={`w-8 h-8 rounded-full flex items-center justify-center text-white text-sm font-bold ${
                      supplier.rating === 'A' ? 'bg-green-500' : supplier.rating === 'B' ? 'bg-orange-500' : 'bg-red-500'
                    }`}>
                      {supplier.rating}
                    </div>
                    <span className="ml-3 font-medium">{supplier.name}</span>
                  </div>
                  <span className="text-sm text-gray-600">{supplier.claims} réclamations</span>
                </div>
              ))}
            </div>
          </div>
        </div>
      </div>
    );
  };

  const ClaimsModule = () => {
    const [searchTerm, setSearchTerm] = useState('');
    const [filterSeverity, setFilterSeverity] = useState('');

    const filteredClaims = claims.filter(claim => {
      const matchesSearch = claim.reclamation_nr?.toLowerCase().includes(searchTerm.toLowerCase()) ||
                           claim.supplier_name?.toLowerCase().includes(searchTerm.toLowerCase()) ||
                           claim.description?.toLowerCase().includes(searchTerm.toLowerCase());
      const matchesSeverity = filterSeverity === '' || claim.severity === filterSeverity;
      return matchesSearch && matchesSeverity;
    });

    return (
      <div className="space-y-6">
        <div className="flex justify-between items-center">
          <h2 className="text-3xl font-bold text-gray-800">Gestion des Réclamations</h2>
          <button 
            onClick={() => setShowNewClaimForm(true)}
            className="bg-red-500 text-white px-4 py-2 rounded-lg flex items-center hover:bg-red-600 transition-colors"
          >
            <Plus className="w-4 h-4 mr-2" />
            Nouvelle Réclamation
          </button>
        </div>

        <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
          <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
            <div className="relative">
              <Search className="w-4 h-4 absolute left-3 top-3 text-gray-400" />
              <input
                type="text"
                placeholder="Rechercher..."
                className="w-full pl-10 pr-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent"
                value={searchTerm}
                onChange={(e) => setSearchTerm(e.target.value)}
              />
            </div>
            <select
              className="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent"
              value={filterSeverity}
              onChange={(e) => setFilterSeverity(e.target.value)}
            >
              <option value="">Toutes les sévérités</option>
              <option value="KRITISCH">Critiques</option>
              <option value="MAJOR">Majeures</option>
              <option value="MINOR">Mineures</option>
            </select>
            <div className="flex gap-2">
              <button className="px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50 flex items-center">
                <Download className="w-4 h-4 mr-2" />
                Exporter
              </button>
            </div>
          </div>
        </div>

        <div className="bg-white rounded-xl shadow-lg border border-gray-100 overflow-hidden">
          <div className="overflow-x-auto">
            <table className="w-full">
              <thead className="bg-gray-50">
                <tr>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">ID / Fournisseur</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Pièce / Description</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Sévérité</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Dommages</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
                </tr>
              </thead>
              <tbody className="bg-white divide-y divide-gray-200">
                {filteredClaims.map((claim) => (
                  <tr key={claim.id} className="hover:bg-gray-50">
                    <td className="px-6 py-4 whitespace-nowrap">
                      <div>
                        <div className="text-sm font-medium text-gray-900">{claim.reclamation_nr}</div>
                        <div className="text-sm text-gray-500">{claim.supplier_name}</div>
                      </div>
                    </td>
                    <td className="px-6 py-4">
                      <div>
                        <div className="text-sm font-medium text-gray-900">{claim.part_number}</div>
                        <div className="text-sm text-gray-500">{claim.description}</div>
                      </div>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <span className={`inline-flex px-2 py-1 text-xs font-semibold rounded-full border ${getSeverityColor(claim.severity)}`}>
                        {claim.severity}
                      </span>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <div className="flex items-center">
                        {getStatusIcon(claim.supplier_status)}
                        <span className="ml-2 text-sm text-gray-900">{claim.supplier_status}</span>
                      </div>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <div className="text-sm font-medium text-gray-900">{(claim.damages_eur || 0).toLocaleString()}€</div>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm font-medium">
                      <button className="text-blue-600 hover:text-blue-900 mr-3">
                        <Eye className="w-4 h-4" />
                      </button>
                      <button className="text-red-600 hover:text-red-900">
                        <Edit className="w-4 h-4" />
                      </button>
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </div>
      </div>
    );
  };

  const CostsModule = () => {
    const totalCosts = costs.reduce((sum, cost) => sum + (cost.amount_eur || 0), 0);
    const totalRecovered = costs.reduce((sum, cost) => sum + (cost.recovered_eur || 0), 0);
    const recoveryRate = totalCosts > 0 ? ((totalRecovered / totalCosts) * 100).toFixed(1) : 0;

    return (
      <div className="space-y-6">
        <div className="flex justify-between items-center">
          <h2 className="text-3xl font-bold text-gray-800">Gestion des Coûts</h2>
          <div className="text-sm text-gray-500">Recovery Rate: {recoveryRate}%</div>
        </div>

        <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm font-medium text-gray-600">Coûts Totaux</p>
                <p className="text-2xl font-bold text-gray-900">{totalCosts.toLocaleString()}€</p>
              </div>
              <div className="w-12 h-12 bg-red-100 rounded-lg flex items-center justify-center">
                <Euro className="w-6 h-6 text-red-600" />
              </div>
            </div>
          </div>

          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm font-medium text-gray-600">Récupéré</p>
                <p className="text-2xl font-bold text-green-600">{totalRecovered.toLocaleString()}€</p>
              </div>
              <div className="w-12 h-12 bg-green-100 rounded-lg flex items-center justify-center">
                <CheckCircle className="w-6 h-6 text-green-600" />
              </div>
            </div>
          </div>

          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm font-medium text-gray-600">À Récupérer</p>
                <p className="text-2xl font-bold text-orange-600">{(totalCosts - totalRecovered).toLocaleString()}€</p>
              </div>
              <div className="w-12 h-12 bg-orange-100 rounded-lg flex items-center justify-center">
                <Clock className="w-6 h-6 text-orange-600" />
              </div>
            </div>
          </div>
        </div>

        <div className="bg-white rounded-xl shadow-lg border border-gray-100 overflow-hidden">
          <div className="px-6 py-4 bg-gray-50 border-b border-gray-200">
            <h3 className="text-lg font-semibold text-gray-800">Détail des Coûts par Réclamation</h3>
          </div>
          <div className="overflow-x-auto">
            <table className="w-full">
              <thead className="bg-gray-50">
                <tr>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">ID Réclamation</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Catégorie</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Montant</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Récupéré</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                </tr>
              </thead>
              <tbody className="bg-white divide-y divide-gray-200">
                {costs.map((cost, index) => (
                  <tr key={index} className="hover:bg-gray-50">
                    <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                      {cost.reclamation_id}
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                      {cost.category}
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                      {(cost.amount_eur || 0).toLocaleString()}€
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-green-600">
                      {(cost.recovered_eur || 0).toLocaleString()}€
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <span className={`inline-flex px-2 py-1 text-xs font-semibold rounded-full ${
                        cost.recovered_eur === cost.amount_eur 
                          ? 'bg-green-100 text-green-800' 
                          : cost.recovered_eur > 0 
                            ? 'bg-yellow-100 text-yellow-800'
                            : 'bg-red-100 text-red-800'
                      }`}>
                        {cost.recovered_eur === cost.amount_eur ? 'Récupéré' : cost.recovered_eur > 0 ? 'Partiel' : 'Pending'}
                      </span>
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </div>
      </div>
    );
  };

  const Track8DModule = () => {
    return (
      <div className="space-y-6">
        <div className="flex justify-between items-center">
          <h2 className="text-3xl font-bold text-gray-800">Suivi 8D Reports</h2>
          <button className="bg-purple-500 text-white px-4 py-2 rounded-lg flex items-center hover:bg-purple-600 transition-colors">
            <FileText className="w-4 h-4 mr-2" />
            Nouveau 8D
          </button>
        </div>

        <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
          {claims.map((claim) => (
            <div key={claim.id} className="bg-white rounded-xl shadow-lg p-6 border border-gray-100">
              <div className="flex justify-between items-start mb-4">
                <div>
                  <h3 className="text-lg font-semibold text-gray-800">{claim.reclamation_nr}</h3>
                  <p className="text-sm text-gray-600">{claim.supplier_name}</p>
                </div>
                <span className={`inline-flex px-2 py-1 text-xs font-semibold rounded-full border ${getSeverityColor(claim.severity)}`}>
                  {claim.severity}
                </span>
              </div>

              <div className="space-y-3">
                <div className="flex justify-between">
                  <span className="text-sm text-gray-600">Date envoi:</span>
                  <span className="text-sm font-medium">{claim.date_sent}</span>
                </div>
                <div className="flex justify-between">
                  <span className="text-sm text-gray-600">Échéance 8D:</span>
                  <span className="text-sm font-medium">{claim.due_8d}</span>
                </div>
                <div className="flex justify-between">
                  <span className="text-sm text-gray-600">8D reçu:</span>
                  <span className="text-sm font-medium">
                    {claim.received_8d || 'En attente'}
                  </span>
                </div>
                <div className="flex justify-between">
                  <span className="text-sm text-gray-600">Status:</span>
                  <div className="flex items-center">
                    {getStatusIcon(claim.supplier_status)}
                    <span className="ml-1 text-sm font-medium">{claim.supplier_status}</span>
                  </div>
                </div>
              </div>

              <div className="mt-4 pt-4 border-t border-gray-200">
                <div className="flex gap-2">
                  <button className="flex-1 px-3 py-2 text-sm bg-blue-50 text-blue-600 rounded-lg hover:bg-blue-100 transition-colors">
                    Voir 8D
                  </button>
                  <button className="flex-1 px-3 py-2 text-sm bg-green-50 text-green-600 rounded-lg hover:bg-green-100 transition-colors">
                    Rappel
                  </button>
                </div>
              </div>
            </div>
          ))}
        </div>
      </div>
    );
  };

  const SuppliersModule = () => {
    return (
      <div className="space-y-6">
        <div className="flex justify-between items-center">
          <h2 className="text-3xl font-bold text-gray-800">Rating Fournisseurs</h2>
          <button className="bg-orange-500 text-white px-4 py-2 rounded-lg flex items-center hover:bg-orange-600 transition-colors">
            <Star className="w-4 h-4 mr-2" />
            Évaluer
          </button>
        </div>

        <div className="bg-white rounded-xl shadow-lg border border-gray-100 overflow-hidden">
          <div className="px-6 py-4 bg-gray-50 border-b border-gray-200">
            <h3 className="text-lg font-semibold text-gray-800">Performance Fournisseurs</h3>
          </div>
          <div className="overflow-x-auto">
            <table className="w-full">
              <thead className="bg-gray-50">
                <tr>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Fournisseur</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Rating</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Réclamations</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">8D À Temps</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Coût Total</th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Recovery</th>
                </tr>
              </thead>
              <tbody className="bg-white divide-y divide-gray-200">
                {suppliers.map((supplier, index) => (
                  <tr key={index} className="hover:bg-gray-50">
                    <td className="px-6 py-4 whitespace-nowrap">
                      <div className="flex items-center">
                        <div className="w-10 h-10 bg-gray-200 rounded-full flex items-center justify-center">
                          <span className="text-sm font-medium text-gray-600">
                            {supplier.name?.split(' ').map(n => n[0]).join('') || 'N/A'}
                          </span>
                        </div>
                        <div className="ml-3">
                          <div className="text-sm font-medium text-gray-900">{supplier.name}</div>
                        </div>
                      </div>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <span className={`inline-flex items-center px-3 py-1 rounded-full text-sm font-medium ${
                        supplier.rating === 'A' ? 'bg-green-100 text-green-800' :
                        supplier.rating === 'B' ? 'bg-yellow-100 text-yellow-800' :
                        'bg-red-100 text-red-800'
                      }`}>
                        <Star className="w-4 h-4 mr-1" />
                        {supplier.rating}
                      </span>
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                      {supplier.claims || 0}
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                      {supplier.on_time_8d_rate || 0}%
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                      {(supplier.total_cost || 0).toLocaleString()}€
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                      {supplier.total_cost > 0 ? (((supplier.recovered || 0) / supplier.total_cost) * 100).toFixed(1) : 0}%
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </div>

        <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100 text-center">
            <div className="w-16 h-16 bg-green-100 rounded-full mx-auto flex items-center justify-center mb-3">
              <Star className="w-8 h-8 text-green-600" />
            </div>
            <h3 className="text-lg font-semibold text-gray-800">Rating A</h3>
            <p className="text-3xl font-bold text-green-600">{suppliers.filter(s => s.rating === 'A').length}</p>
            <p className="text-sm text-gray-600">Excellents fournisseurs</p>
          </div>

          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100 text-center">
            <div className="w-16 h-16 bg-yellow-100 rounded-full mx-auto flex items-center justify-center mb-3">
              <Star className="w-8 h-8 text-yellow-600" />
            </div>
            <h3 className="text-lg font-semibold text-gray-800">Rating B</h3>
            <p className="text-3xl font-bold text-yellow-600">{suppliers.filter(s => s.rating === 'B').length}</p>
            <p className="text-sm text-gray-600">À surveiller</p>
          </div>

          <div className="bg-white rounded-xl shadow-lg p-6 border border-gray-100 text-center">
            <div className="w-16 h-16 bg-red-100 rounded-full mx-auto flex items-center justify-center mb-3">
              <Star className="w-8 h-8 text-red-600" />
            </div>
            <h3 className="text-lg font-semibold text-gray-800">Rating C</h3>
            <p className="text-3xl font-bold text-red-600">{suppliers.filter(s => s.rating === 'C').length}</p>
            <p className="text-sm text-gray-600">Action requise</p>
          </div>
        </div>
      </div>
    );
  };

  const renderActiveModule = () => {
    switch(activeModule) {
      case 'dashboard': return <DashboardModule />;
      case 'claims': return <ClaimsModule />;
      case 'costs': return <CostsModule />;
      case '8d': return <Track8DModule />;
      case 'suppliers': return <SuppliersModule />;
      default: return <DashboardModule />;
    }
  };

  if (loading) {
    return (
      <div className="min-h-screen bg-gray-100 flex items-center justify-center">
        <div className="text-center">
          <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-red-500 mx-auto"></div>
          <p className="mt-4 text-gray-600">Chargement du système...</p>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gray-100">
      <header className="bg-white shadow-lg border-b border-gray-200">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex justify-between items-center py-6">
            <div className="flex items-center">
              <div className="w-10 h-10 bg-gradient-to-r from-red-500 to-orange-500 rounded-lg flex items-center justify-center">
                <AlertTriangle className="w-6 h-6 text-white" />
              </div>
              <div className="ml-4">
                <h1 className="text-2xl font-bold text-gray-900">AutoClaims Pro</h1>
                <p className="text-sm text-gray-600">Système de Gestion des Réclamations Automotive</p>
              </div>
            </div>
            <div className="flex items-center space-x-4">
              <div className="text-sm text-gray-600">
                <span className="font-medium">ROI:</span> 58 min économisées/réclamation
              </div>
              <div className="w-8 h-8 bg-gray-300 rounded-full flex items-center justify-center">
                <Users className="w-4 h-4 text-gray-600" />
              </div>
            </div>
          </div>
        </div>
      </header>

      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div className="flex gap-8">
          <div className="w-64 space-y-2">
            {modules.map((module) => {
              const IconComponent = module.icon;
              return (
                <button
                  key={module.id}
                  onClick={() => setActiveModule(module.id)}
                  className={`w-full flex items-center px-4 py-3 rounded-xl text-left transition-colors ${
                    activeModule === module.id
                      ? 'bg-white shadow-lg text-gray-900'
                      : 'text-gray-600 hover:bg-white hover:shadow-md'
                  }`}
                >
                  <div className={`w-8 h-8 rounded-lg flex items-center justify-center mr-3 ${
                    activeModule === module.id ? module.color : 'bg-gray-200'
                  }`}>
                    <IconComponent className={`w-4 h-4 ${
                      activeModule === module.id ? 'text-white' : 'text-gray-600'
                    }`} />
                  </div>
                  <span className="font-medium">{module.name}</span>
                </button>
              );
            })}
          </div>

          <div className="flex-1">
            {renderActiveModule()}
          </div>
        </div>
      </div>

      {showNewClaimForm && (
        <NewClaimForm 
          onSubmit={createClaim}
          onCancel={() => setShowNewClaimForm(false)}
        />
      )}
    </div>
  );
};

export default AutoClaimsSystem;
