# strom-zivota
import React, { useState } from 'react';
import { Card, CardContent } from '@/components/ui/card';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { PlusCircle, ImagePlus, Edit } from 'lucide-react';
import { motion } from 'framer-motion';

const FamilyTree = () => {
    const [family, setFamily] = useState([
        { id: 1, name: 'Já', relation: 'Já', photo: '', note: '', children: [] }
    ]);

    const addFamilyMember = (id) => {
        const newMember = { id: Date.now(), name: '', relation: '', photo: '', note: '', children: [] };
        const addMember = (nodes) => {
            return nodes.map(node => {
                if (node.id === id) {
                    return { ...node, children: [...node.children, newMember] };
                } else if (node.children.length) {
                    return { ...node, children: addMember(node.children) };
                }
                return node;
            });
        };
        setFamily(addMember(family));
    };

    const updateMember = (id, key, value) => {
        const updateNodes = (nodes) => {
            return nodes.map(node => {
                if (node.id === id) {
                    return { ...node, [key]: value };
                } else if (node.children.length) {
                    return { ...node, children: updateNodes(node.children) };
                }
                return node;
            });
        };
        setFamily(updateNodes(family));
    };

    const renderTree = (nodes) => {
        return nodes.map(node => (
            <div key={node.id} className="my-4 ml-4">
                <Card className="p-4 rounded-2xl shadow-lg bg-green-100 relative">
                    <CardContent>
                        <Input
                            placeholder="Jméno"
                            value={node.name}
                            onChange={(e) => updateMember(node.id, 'name', e.target.value)}
                            className="mb-2"
                        />
                        <Input
                            placeholder="Vztah (např. kamarád, vychovatel, mentor)"
                            value={node.relation}
                            onChange={(e) => updateMember(node.id, 'relation', e.target.value)}
                            className="mb-2"
                        />
                        <textarea
                            placeholder="Poznámky (např. oblíbené aktivity)"
                            value={node.note}
                            onChange={(e) => updateMember(node.id, 'note', e.target.value)}
                            className="w-full p-2 mb-2 rounded-lg border"
                        />
                        <Button variant="outline" className="mb-2" onClick={() => updateMember(node.id, 'photo', prompt('Vlož odkaz na fotku'))}>
                            <ImagePlus className="mr-2" /> Přidat fotku
                        </Button>
                        {node.photo && <img src={node.photo} alt="foto" className="w-20 h-20 object-cover rounded-full mb-2" />}
                        <Button variant="outline" className="mt-4" onClick={() => addFamilyMember(node.id)}>
                            <PlusCircle className="mr-2" /> Přidat člena
                        </Button>
                    </CardContent>
                </Card>
                <motion.div layout>
                    {node.children.length > 0 && <div className="ml-8 mt-4">{renderTree(node.children)}</div>}
                </motion.div>
            </div>
        ));
    };

    return <div className="p-8 min-h-screen bg-cover bg-no-repeat bg-center" style={{ backgroundImage: 'url(https://images.unsplash.com/photo-1526509174580-8ce20843e5c7)' }}>{renderTree(family)}</div>;
};

export default FamilyTree;
