---
layout: post
read_time: true
show_date: true
title: "Calculating Hartree Fock Energy of Hydrogen Using Python"
date: 2021-09-02
img: posts/20210324/starting_adventure.jpg
tags: [general blogging, thoughts, life]
author: Tianyou Mou
description: "Midlife career change: a disaster or an opportunity?"
---

## Overview

The lattice convolutional neural network (LCNN) is a Python library for deep learning of lattice system developed by the Vlachos group at the University of Delaware and Jung group at KAIST. The model has been built in hope to improve upon cluster expansion methods, linear regression based on the clusters in the lattice. The LCNN performs better than the cluster expansion and cluster expansion based machine learning methods with sufficient number of data points.

I employed this LCNN to train the Cluster expansion model and then compare with the CE-TinNet model. 

Import needed libraries
```python
import numpy as np 
import collections 
import copy
from scipy.special import comb
from scipy.special import factorial2
from scipy.special import factoria
```
Read Basis set info from the EMSL basis set exchange in the format of Gaussian 94. 

```python
file = open("basis.dat")
aodict = collections.defaultdict(list)
newatomtype = False
contract_num = None
origin = (0,0,0)
orbitalType = None
for line in file:
    if line.strip() == "****":
        newatomtype = True
        linecount = 0
    elif line.strip() != "****" and newatomtype == True:
        if linecount == 0:
            atomtype, _ = line.split()
            linecount += 1
            print(atomtype)
        elif linecount == 1:
            orbitalType, contract_num, _ = line.split()
            contract_num = int(contract_num)
            if orbitalType == "S":
                aos = Ao(origin, (0,0,0), contract_num)
                aodict[atomtype].append(aos)
            elif orbitalType == "P":
                aopx = Ao(origin,(1,0,0), contract_num)
                aopy = Ao(origin,(0,1,0), contract_num)
                aopz = Ao(origin,(0,0,1), contract_num)
                aodict[atomtype].append(aopx)
                aodict[atomtype].append(aopy)
                aodict[atomtype].append(aopz)
            elif orbitalType == "D":
                aodx2 = Ao(origin,(2,0,0), contract_num)
                aody2 = Ao(origin,(0,2,0), contract_num)
                aodz2 = Ao(origin,(0,0,2), contract_num)
                aodxy = Ao(origin,(1,1,0), contract_num)
                aodyz = Ao(origin,(0,1,1), contract_num)
                aodzx = Ao(origin,(1,0,1), contract_num)
                aodict[atomtype].append(aodx2)
                aodict[atomtype].append(aody2)
                aodict[atomtype].append(aodz2)
                aodict[atomtype].append(aodxy)
                aodict[atomtype].append(aodyz)
                aodict[atomtype].append(aodzx)
            elif orbitalType == "SP":
                aos = Ao(origin,(0,0,0), contract_num)
                aodict[atomtype].append(aos)
                aopx = Ao(origin,(1,0,0), contract_num)
                aopy = Ao(origin,(0,1,0), contract_num)
                aopz = Ao(origin,(0,0,1), contract_num)
                aodict[atomtype].append(aopx)
                aodict[atomtype].append(aopy)
                aodict[atomtype].append(aopz)
            linecount += 1
            print(orbitalType, contract_num)
        elif contract_num and 1 <linecount <= 1 + contract_num:
            if orbitalType == "S" or orbitalType == "P" or orbitalType == "D":
                exponent, coeff = line.split()
                exponent = float(exponent)
                coeff = float(coeff)
                if orbitalType == "S":
                    aos.exponents[linecount - 2] = exponent
                    aos.coeffs[linecount - 2] = coeff
                elif orbitalType == "P":
                    aopx.exponents[linecount - 2] = exponent
                    aopy.exponents[linecount - 2] = exponent
                    aopz.exponents[linecount - 2] = exponent
                    aopx.coeffs[linecount - 2] = coeff
                    aopy.coeffs[linecount - 2] = coeff
                    aopz.coeffs[linecount - 2] = coeff
                elif orbitalType == "D":
                    aodx2.exponents[linecount - 2] = exponent
                    aody2.exponents[linecount - 2] = exponent
                    aodz2.exponents[linecount - 2] = exponent
                    aodxy.exponents[linecount - 2] = exponent
                    aodyz.exponents[linecount - 2] = exponent
                    aodzx.exponents[linecount - 2] = exponent
                    aodx2.coeffs[linecount - 2] = coeff
                    aody2.coeffs[linecount - 2] = coeff
                    aodz2.coeffs[linecount - 2] = coeff
                    aodxy.coeffs[linecount - 2] = coeff
                    aodyz.coeffs[linecount - 2] = coeff
                    aodzx.coeffs[linecount - 2] = coeff
                print(exponent, coeff)
            elif orbitalType == "SP":
                exponent, coeffs, coeffp = line.split()
                exponent = float(exponent)
                coeffs = float(coeffs)
                coeffp = float(coeffp)
                aos.exponents[linecount - 2] = exponent
                aos.coeffs[linecount - 2] = coeffs
                aopx.exponents[linecount - 2] = exponent
                aopy.exponents[linecount - 2] = exponent
                aopz.exponents[linecount - 2] = exponent
                aopx.coeffs[linecount - 2] = coeffp
                aopy.coeffs[linecount - 2] = coeffp
                aopz.coeffs[linecount - 2] = coeffp
                print(exponent, coeffs, coeffp)
            if linecount < 1 + int(contract_num):
                linecount += 1
            else:
                linecount = 1
                
print(aodict)
```
```
H
S 2
5.447178 0.1562849787
0.82454724 0.9046908767
S 1
0.18319158 1.0
O
S 3
322.037 0.05923939339
48.4308 0.3514999608
10.4206 0.707657921
SP 2
7.40294 -0.4044535832 0.244586107
1.5762 1.221561761 0.8539553735
SP 1
0.373684 1.0 1.0
defaultdict(<class 'list'>, {'H': [S(0, 0, 0)[5.447178, 0.82454724][0.1562849787, 0.9046908767], S(0, 0, 0)[0.18319158][1.0]], 'O': [S(0, 0, 0)[322.037, 48.4308, 10.4206][0.05923939339, 0.3514999608, 0.707657921], S(0, 0, 0)[7.40294, 1.5762][-0.4044535832, 1.221561761], Px(0, 0, 0)[7.40294, 1.5762][0.244586107, 0.8539553735], Py(0, 0, 0)[7.40294, 1.5762][0.244586107, 0.8539553735], Pz(0, 0, 0)[7.40294, 1.5762][0.244586107, 0.8539553735], S(0, 0, 0)[0.373684][1.0], Px(0, 0, 0)[0.373684][1.0], Py(0, 0, 0)[0.373684][1.0], Pz(0, 0, 0)[0.373684][1.0]]})
```

```python
geomfile = open("geom.dat")
aolist = []
atomlist = []
for line in geomfile:
    atomtype, x, y, z = line.split()
    x = float(x)
    y = float(y)
    z = float(z)
    x = x * 1.8897261339213 # from Angstrom to atomic unit
    y = y * 1.8897261339213 # from Angstrom to atomic unit
    z = z * 1.8897261339213 # ....
    atomlist.append((atomtype, (x, y, z)))
    if atomtype in aodict:
        for ao in aodict[atomtype]:
            aonew = copy.copy(ao)
            aonew.center = [x,y,z]
            aolist.append(aonew)
            
print(aolist)
print(len(aolist))
```
```
[S[-0.8398320884373042, 0.75589045356852, 0.0][322.037, 48.4308, 10.4206][0.05923939339, 0.3514999608, 0.707657921], S[-0.8398320884373042, 0.75589045356852, 0.0][7.40294, 1.5762][-0.4044535832, 1.221561761], Px[-0.8398320884373042, 0.75589045356852, 0.0][7.40294, 1.5762][0.244586107, 0.8539553735], Py[-0.8398320884373042, 0.75589045356852, 0.0][7.40294, 1.5762][0.244586107, 0.8539553735], Pz[-0.8398320884373042, 0.75589045356852, 0.0][7.40294, 1.5762][0.244586107, 0.8539553735], S[-0.8398320884373042, 0.75589045356852, 0.0][0.373684][1.0], Px[-0.8398320884373042, 0.75589045356852, 0.0][0.373684][1.0], Py[-0.8398320884373042, 0.75589045356852, 0.0][0.373684][1.0], Pz[-0.8398320884373042, 0.75589045356852, 0.0][0.373684][1.0], S[0.9932022614663569, 0.75589045356852, 0.0][5.447178, 0.82454724][0.1562849787, 0.9046908767], S[0.9932022614663569, 0.75589045356852, 0.0][0.18319158][1.0], S[-1.616887474705743, 1.9434321506473433, 1.1601406681369646][5.447178, 0.82454724][0.1562849787, 0.9046908767], S[-1.616887474705743, 1.9434321506473433, 1.1601406681369646][0.18319158][1.0]]
13
```

Define the function that calculates 1-electron and 2-electron integrals.

```python
def gint(m, center1, exponent1, n, center2, exponent2): #calculate one electron integral of two gaussian primitives
    newcenter = (exponent1 * center1 + exponent2 * center2) / (exponent1 + exponent2)
    newexponent = exponent1 + exponent2
    tempexponent = exponent1 * exponent2 / (exponent1 + exponent2)
    e12 = np.exp(-tempexponent*(center1 - center2)**2)
    res = 0
    for i in range(m + 1):
        for j in range(n + 1):
            if (i + j) % 2 == 0:
                res += (np.sqrt(np.pi/newexponent)*comb(m, i)*comb(n, j)*
                        factorial2(i + j - 1)/(2*newexponent)**((i + j)/2)*
                        (newcenter - center1)**(m - i)*(newcenter - center2)**(n - j)
                       )
    res = e12 * res
    return res
                        

def E(l1, l2, t, center1, center2, exponent1, exponent2):#calculate the gaussian-hermite expansion coefficient using recurence
    newcenter = (exponent1 * center1 + exponent2 * center2) / (exponent1 + exponent2)
    sumexponent = exponent1 + exponent2
    diffcenter = center1 - center2
    redexponent = exponent1 * exponent2 / (exponent1 + exponent2)
    if t > l1 + l2:
        return 0
    if l1 < 0 or l2 < 0 or t < 0:
        return 0
    elif l1 == 0 and l2 == 0 and t == 0:
        return np.exp(-redexponent*diffcenter**2)
    elif l1 > 0:
        return (1/(2*sumexponent)*E(l1-1, l2, t - 1,center1,center2,exponent1,exponent2)
                +(newcenter - center1)*E(l1-1,l2,t,center1,center2,exponent1,exponent2)
                +(t + 1)*E(l1-1,l2, t+1, center1,center2,exponent1,exponent2))
    elif l1 == 0:
        return (1/(2*sumexponent)*E(l1, l2-1, t - 1,center1,center2,exponent1,exponent2)
                +(newcenter - center2)*E(l1,l2-1,t,center1,center2,exponent1,exponent2)
                +(t + 1)*E(l1,l2-1, t+1, center1,center2,exponent1,exponent2))
    return 0


def S(m1, m2, center1, center2, exponent1, exponent2): #calculate overlap type integral
    return np.sqrt(np.pi/(exponent1 + exponent2))*E(m1, m2, 0, center1, center2, exponent1, exponent2)


def T(m1, m2, center1, center2, exponent1, exponent2): #calculate kinetic type integral
    res = 0
    res += -2*exponent2*S(m1, m2 + 2, center1, center2, exponent1, exponent2)
    res += exponent2*(2*m2+1)*S(m1, m2, center1, center2, exponent1, exponent2)
    res += -1/2*m2*(m2-1)*S(m1, m2 - 2, center1, center2, exponent1, exponent2)
    return res
    

def F(n, x): #calculate Boys function value by numerical integration
    if x < 1e-7:
        return 1/(2*n + 1)
    if n == 20:
        res1 = 1/(2*n + 1)
        #if x < 1e-7:
        #    return res1
        for k in range(1,11):
            res1 += (-x)**k/factorial(k)/(2*n+2*k+1)
        res2 = factorial2(2*n-1)/2**(n+1)*np.sqrt(np.pi/x**(2*n+1))
        res = min(res1, res2)
        return res
    return (2*x*F(n+1,x)+np.exp(-x))/(2*n+1)


def R(t, u, v, n, p, x, y, z):
    if t < 0 or u < 0 or v < 0:
        return 0
    if t == 0 and u == 0 and v == 0:
        return (-2*p)**n*F(n,p*(x**2+y**2+z**2))
    if t > 0:
        return (t-1)*R(t-2,u,v,n+1,p,x,y,z)+x*R(t-1,u,v,n+1,p,x,y,z)
    if u > 0:
        return (u-1)*R(t,u-2,v,n+1,p,x,y,z)+y*R(t,u-1,v,n+1,p,x,y,z)
    if v > 0:
        return (v-1)*R(t,u,v-2,n+1,p,x,y,z)+z*R(t,u,v-1,n+1,p,x,y,z)
    
    
def overlap(ao1, ao2): #calculate overlap matrix <psi|phi>
    l1, m1, n1 = ao1.angular
    l2, m2, n2 = ao2.angular
    x1, y1, z1 = ao1.center
    x2, y2, z2 = ao2.center
    res = 0
    for i in range(len(ao1.coeffs)):
        for j in range(len(ao2.coeffs)):
            exponent1 = ao1.exponents[i]
            exponent2 = ao2.exponents[j]
            #res += (ao1.coeffs[i]*ao2.coeffs[j]*
            #        gint(l1, x1, exponent1, l2, x2, exponent2)*
            #        gint(m1, y1, exponent1, m2, y2, exponent2)*
            #        gint(n1, z1, exponent1, n2, z2, exponent2))
            res += (ao1.coeffs[i]*ao2.coeffs[j]*
                    S(l1, l2, x1, x2, exponent1, exponent2)*
                    S(m1, m2, y1, y2, exponent1, exponent2)*
                    S(n1, n2, z1, z2, exponent1, exponent2))
    return res




def kinetic(ao1, ao2): #calculate kinetic integral <psi|-1/2*del^2|phi>
    l1, m1, n1 = ao1.angular
    l2, m2, n2 = ao2.angular
    x1, y1, z1 = ao1.center
    x2, y2, z2 = ao2.center
    res = 0
    for i in range(len(ao1.coeffs)):
        for j in range(len(ao2.coeffs)):
            exponent1 = ao1.exponents[i]
            exponent2 = ao2.exponents[j]
            res += (ao1.coeffs[i]*ao2.coeffs[j]*
                    (T(l1,l2,x1,x2,exponent1,exponent2)*S(m1,m2,y1,y2,exponent1,exponent2)*S(n1,n2,z1,z2,exponent1,exponent2) +
                     S(l1,l2,x1,x2,exponent1,exponent2)*T(m1,m2,y1,y2,exponent1,exponent2)*S(n1,n2,z1,z2,exponent1,exponent2) +
                     S(l1,l2,x1,x2,exponent1,exponent2)*S(m1,m2,y1,y2,exponent1,exponent2)*T(n1,n2,z1,z2,exponent1,exponent2))
                   )
    return res


def oneelectron(ao1, centerC, ao2):
    l1, m1, n1 = ao1.angular
    l2, m2, n2 = ao2.angular
    a = l1 + m1 + n1
    b = l2 + m2 + n2
    c = a + b
    x1, y1, z1 = ao1.center
    x2, y2, z2 = ao2.center
    xc, yc, zc = centerC # coordinate of atom with charge Z
    res = 0
    for i in range(len(ao1.coeffs)):
        for j in range(len(ao2.coeffs)):
            exponent1 = ao1.exponents[i]
            exponent2 = ao2.exponents[j]
            p = exponent1 + exponent2
            xp = (exponent1*x1+exponent2*x2)/p
            yp = (exponent1*y1+exponent2*y2)/p
            zp = (exponent1*z1+exponent2*z2)/p
            xpc = xp - xc
            ypc = yp - yc
            zpc = zp - zc
            for t in range(c+1):
                for u in range(c+1):
                    for v in range(c+1):
                        res += (ao1.coeffs[i]*ao2.coeffs[j]*
                                2*np.pi/p*E(l1,l2,t,x1,x2,exponent1,exponent2)*
                                E(m1,m2,u,y1,y2,exponent1,exponent2)*
                                E(n1,n2,v,z1,z2,exponent1,exponent2)*
                                R(t,u,v,0,p,xpc,ypc,zpc))
    return res
                  
    
def twoelectron(ao1, ao2, ao3, ao4):
    res = 0
    l1, m1, n1 = ao1.angular
    l2, m2, n2 = ao2.angular
    l3, m3, n3 = ao3.angular
    l4, m4, n4 = ao4.angular
    x1, y1, z1 = ao1.center
    x2, y2, z2 = ao2.center
    x3, y3, z3 = ao3.center
    x4, y4, z4 = ao4.center
    a = l1 + m1 + n1
    b = l2 + m2 + n2
    c = l3 + m3 + n3
    d = l4 + m4 + n4
    for i in range(len(ao1.coeffs)):
        for j in range(len(ao2.coeffs)):
            for k in range(len(ao3.coeffs)):
                for l in range(len(ao4.coeffs)):
                    exponent1 = ao1.exponents[i]
                    exponent2 = ao2.exponents[j]
                    exponent3 = ao3.exponents[k]
                    exponent4 = ao4.exponents[l]
                    p = (exponent1 + exponent2)
                    q = (exponent3 + exponent4)
                    alpha = p*q/(p + q)
                    xp = (x1*exponent1+x2*exponent2)/p
                    yp = (y1*exponent1+y2*exponent2)/p
                    zp = (z1*exponent1+z2*exponent2)/p
                    xq = (x3*exponent3+x4*exponent4)/q
                    yq = (y3*exponent3+y4*exponent4)/q
                    zq = (z3*exponent3+z4*exponent4)/q
                    xpq = xp - xq
                    ypq = yp - yq
                    zpq = zp - zq
                    for t in range(a + b + 1):
                        for u in range(a + b + 1):
                            for v in range(a + b + 1):
                                for tau in range(c + d + 1):
                                    for miu in range(c + d + 1):
                                        for phi in range(c + d + 1):
                                            res += (ao1.coeffs[i]*ao2.coeffs[j]*ao3.coeffs[k]*ao4.coeffs[l]*
                                                    2*np.pi**(5/2)/p/q/np.sqrt(p+q)*
                                                    E(l1, l2, t, x1, x2, exponent1, exponent2)*
                                                    E(m1, m2, u, y1, y2, exponent1, exponent2)*
                                                    E(n1, n2, v, z1, z2, exponent1, exponent2)*
                                                    E(l3, l4, tau, x3, x4, exponent3, exponent4)*
                                                    E(m3, m4, miu, y3, y4, exponent3, exponent4)*
                                                    E(n3, n4, phi, z3, z4, exponent3, exponent4)*
                                                    (-1)**(tau + miu + phi)*
                                                    R(t+tau, u+miu, v+phi, 0, alpha, xpq, ypq, zpq)
                                                    )
    return res
                            
    
    
def atomtype2Charge(atomtype):
    if atomtype == "O":
        return 8
    if atomtype == "H":
        return 1
    if atomtype == "C":
        return 6
    if atomtype == "N":
        return 7
    if atomtype == "S":
        return 16
    
def coulombicAttraction(ao1, atomlist, ao2):
    res = 0
    for i in range(len(atomlist)):
        atomtype, centerC = atomlist[i]
        Z = atomtype2Charge(atomtype)
        res += (-Z)*oneelectron(ao1, centerC, ao2)
    return res

```
Overlap integral
```python
n = len(aolist)
Smatrix = np.zeros((n,n))
for i in range(n):
    for j in range(n):
        Smatrix[i][j] = overlap(aolist[i], aolist[j])
    
print(Smatrix)
```
```
[[ 3.62762641e-02  9.95724217e-02 -5.24746189e-19  7.57023956e-18
   0.00000000e+00  1.16908716e-01 -6.31013314e-19 -1.16984701e-17
   0.00000000e+00  7.59400626e-03  6.54822558e-02  7.59411217e-03
   6.54824705e-02]
 [ 9.95724217e-02  1.29603778e+00 -1.07089269e-18  2.14434851e-18
   0.00000000e+00  2.39434918e+00  0.00000000e+00  0.00000000e+00
   0.00000000e+00  2.62125225e-01  1.62011663e+00  2.62127794e-01
   1.62012145e+00]
 [-5.24746189e-19 -1.07089269e-18  1.20081379e-01 -1.06558994e-33
   0.00000000e+00  0.00000000e+00  4.51862544e-01  0.00000000e+00
   0.00000000e+00  1.19818229e-01  2.25631906e-01 -5.07935538e-02
  -9.56496053e-02]
 [ 7.57023956e-18  2.14434851e-18 -1.06558994e-33  1.20081379e-01
   0.00000000e+00  0.00000000e+00  0.00000000e+00  4.51862544e-01
   0.00000000e+00 -4.79226865e-19  1.26359949e-16  7.76256934e-02
   1.46177347e-01]
 [ 0.00000000e+00  0.00000000e+00  0.00000000e+00  0.00000000e+00
   1.20081379e-01  0.00000000e+00  0.00000000e+00  0.00000000e+00
   4.51862544e-01  0.00000000e+00  0.00000000e+00  7.58345782e-02
   1.42804488e-01]
 [ 1.16908716e-01  2.39434918e+00  0.00000000e+00  0.00000000e+00
   0.00000000e+00  8.61832884e+00  0.00000000e+00  0.00000000e+00
   0.00000000e+00  1.63787924e+00  8.86555994e+00  1.63788694e+00
   8.86557978e+00]
 [-6.31013314e-19  0.00000000e+00  4.51862544e-01  0.00000000e+00
   0.00000000e+00  0.00000000e+00  5.76578663e+00  0.00000000e+00
   0.00000000e+00  2.07467387e+00  5.34594035e+00 -8.79494754e-01
  -2.26624288e+00]
 [-1.16984701e-17  0.00000000e+00  0.00000000e+00  4.51862544e-01
   0.00000000e+00  0.00000000e+00  0.00000000e+00  5.76578663e+00
   0.00000000e+00 -1.77591852e-16 -9.84274877e-16  1.34409556e+00
   3.46340552e+00]
 [ 0.00000000e+00  0.00000000e+00  0.00000000e+00  0.00000000e+00
   4.51862544e-01  0.00000000e+00  0.00000000e+00  0.00000000e+00
   5.76578663e+00  0.00000000e+00  0.00000000e+00  1.31308225e+00
   3.38349180e+00]
 [ 7.59400626e-03  2.62125225e-01  1.19818229e-01 -4.79226865e-19
   0.00000000e+00  1.63787924e+00  2.07467387e+00 -1.77591852e-16
   0.00000000e+00  2.25610663e+00  5.04483670e+00  4.17535237e-02
   1.19856776e+00]
 [ 6.54822558e-02  1.62011663e+00  2.25631906e-01  1.26359949e-16
   0.00000000e+00  8.86555994e+00  5.34594035e+00 -9.84274877e-16
   0.00000000e+00  5.04483670e+00  2.51085454e+01  1.19856776e+00
   1.04515576e+01]
 [ 7.59411217e-03  2.62127794e-01 -5.07935538e-02  7.76256934e-02
   7.58345782e-02  1.63788694e+00 -8.79494754e-01  1.34409556e+00
   1.31308225e+00  4.17535237e-02  1.19856776e+00  2.25610663e+00
   5.04483670e+00]
 [ 6.54824705e-02  1.62012145e+00 -9.56496053e-02  1.46177347e-01
   1.42804488e-01  8.86557978e+00 -2.26624288e+00  3.46340552e+00
   3.38349180e+00  1.19856776e+00  1.04515576e+01  5.04483670e+00
   2.51085454e+01]]
```

Kinetic integral.

```python
Tmatrix = np.zeros((n,n))
for i in range(n):
    for j in range(n):
        Tmatrix[i][j] = kinetic(aolist[i], aolist[j])
print(Tmatrix)
```

```
[[ 1.56594565e+00  4.78963614e-02 -4.05282580e-18  1.53233094e-17
   0.00000000e+00  1.19389125e-01 -1.15463480e-18 -1.97468072e-17
   0.00000000e+00 -1.91652006e-02 -4.52331856e-02 -1.91652712e-02
  -4.52329116e-02]
 [ 3.43170409e+00  2.67654637e+00  1.70224646e-16 -1.33174640e-16
   0.00000000e+00  1.26286426e+00  0.00000000e+00  0.00000000e+00
   0.00000000e+00 -2.25668938e-01 -1.21972362e+00 -2.25667652e-01
  -1.21971864e+00]
 [-7.37006012e-17 -1.17093927e-16  7.22035184e-01 -2.01906383e-32
   0.00000000e+00  0.00000000e+00  4.14183795e-01  0.00000000e+00
   0.00000000e+00  5.48239591e-02  2.35206952e-01 -2.32417873e-02
  -9.97091597e-02]
 [ 2.03161828e-16  1.38250474e-16 -2.01906383e-32  7.22035184e-01
   0.00000000e+00  0.00000000e+00  0.00000000e+00  4.14183795e-01
   0.00000000e+00  1.20452080e-18 -1.22044396e-16  3.55194649e-02
   1.52381396e-01]
 [ 0.00000000e+00  0.00000000e+00  0.00000000e+00  0.00000000e+00
   7.22035184e-01  0.00000000e+00  0.00000000e+00  0.00000000e+00
   4.14183795e-01  0.00000000e+00  0.00000000e+00  3.46998980e-02
   1.48865387e-01]
 [ 4.02359884e+00  3.74487537e+00  0.00000000e+00  0.00000000e+00
   0.00000000e+00 -3.26589848e+00  0.00000000e+00  0.00000000e+00
   0.00000000e+00  4.62021032e-02 -8.79149846e+00  4.62074924e-02
  -8.79149150e+00]
 [-8.33582092e-17  0.00000000e+00  1.84953836e+00  0.00000000e+00
   0.00000000e+00  0.00000000e+00 -3.64155552e+00  0.00000000e+00
   0.00000000e+00  1.45231763e+00  1.87336406e+00 -6.15668633e-01
  -7.94160441e-01]
 [-2.36657541e-16  0.00000000e+00  0.00000000e+00  1.84953836e+00
   0.00000000e+00  0.00000000e+00  0.00000000e+00 -3.64155552e+00
   0.00000000e+00  2.95102321e-16  1.62363285e-15  9.40900978e-01
   1.21368265e+00]
 [ 0.00000000e+00  0.00000000e+00  0.00000000e+00  0.00000000e+00
   1.84953836e+00  0.00000000e+00  0.00000000e+00  0.00000000e+00
  -3.64155552e+00  0.00000000e+00  0.00000000e+00  9.19190873e-01
   1.18567845e+00]
 [ 2.54823773e-01  2.67169469e-01  4.21218474e-01 -1.53429924e-17
   0.00000000e+00 -1.64897744e+00 -1.81824719e+00  1.56768583e-16
   0.00000000e+00  2.94423203e+00  5.04566129e-02 -1.23635777e-01
  -2.81387679e+00]
 [ 2.23804340e+00  2.09632432e+00  7.87206118e-01  3.00934224e-16
   0.00000000e+00 -1.03178173e+01 -9.40091335e+00  1.73086159e-15
   0.00000000e+00  9.69998131e-01 -2.38637957e+01 -4.36962560e-01
  -1.90937675e+01]
 [ 2.54827376e-01  2.67173409e-01 -1.78564047e-01  2.72892069e-01
   2.66595428e-01 -1.64897455e+00  7.70784733e-01 -1.17795852e+00
  -1.15077861e+00 -1.23635777e-01 -2.81387679e+00  2.94423203e+00
   5.04566129e-02]
 [ 2.23805076e+00  2.09633131e+00 -3.33711532e-01  5.09997570e-01
   4.98230018e-01 -1.03178274e+01  3.98521751e+00 -6.09044355e+00
  -5.94991424e+00 -4.36962560e-01 -1.90937675e+01  9.69998131e-01
  -2.38637957e+01]]
```

```python
coulombAttractionMatrix = np.zeros((n,n))
for i in range(n):
    for j in range(n):
        coulombAttractionMatrix[i][j] = coulombicAttraction(aolist[i], atomlist, aolist[j])
print(coulombAttractionMatrix)
```

```
[[-1.75499944e+00 -3.23464624e+00 -6.50279513e-04 -7.31307144e-04
  -7.14433153e-04 -3.79435897e+00 -8.94041634e-04 -1.00544332e-03
  -9.82243984e-04 -2.35608226e-01 -2.09923813e+00 -2.35611600e-01
  -2.09924507e+00]
 [-3.23464624e+00 -2.18381731e+01 -5.61648851e-02 -6.31632831e-02
  -6.17058699e-02 -3.16096986e+01 -1.13502598e-01 -1.27643676e-01
  -1.24698459e-01 -3.13830005e+00 -2.00748303e+01 -3.13833427e+00
  -2.00748975e+01]
 [-6.50279513e-04 -5.61648851e-02 -1.58622060e+00  4.90812422e-03
   4.79487544e-03 -8.10055273e-02 -4.37742871e+00  1.58149688e-02
   1.54500583e-02 -1.17389395e+00 -2.12548559e+00  4.86228914e-01
   8.27361978e-01]
 [-7.31307144e-04 -6.31632831e-02  4.90812422e-03 -1.57263886e+00
  -7.32781037e-03 -9.10978970e-02  1.58149688e-02 -4.33366509e+00
  -2.36116868e-02 -9.01200465e-03 -5.81850271e-02 -7.64342556e-01
  -1.40167890e+00]
 [-7.14433153e-04 -6.17058699e-02  4.79487544e-03 -7.32781037e-03
  -1.57229670e+00 -8.89959277e-02  1.54500583e-02 -2.36116868e-02
  -4.33256260e+00 -8.80406399e-03 -5.68424809e-02 -7.46706314e-01
  -1.36933693e+00]
 [-3.79435897e+00 -3.16096986e+01 -8.10055273e-02 -9.10978970e-02
  -8.89959277e-02 -7.64247324e+01 -8.20332421e-01 -9.22545531e-01
  -9.01258955e-01 -1.29565593e+01 -6.57448692e+01 -1.29566416e+01
  -6.57450367e+01]
 [-8.94041634e-04 -1.13502598e-01 -4.37742871e+00  1.58149688e-02
   1.54500583e-02 -8.20332421e-01 -3.63920009e+01  3.02820982e-01
   2.95833770e-01 -1.41485068e+01 -2.99722078e+01  5.88600845e+00
   1.17045986e+01]
 [-1.00544332e-03 -1.27643676e-01  1.58149688e-02 -4.33366509e+00
  -2.36116868e-02 -9.22545531e-01  3.02820982e-01 -3.55540295e+01
  -4.52110548e-01 -8.83188572e-02 -7.90717729e-01 -9.20368420e+00
  -1.97529156e+01]
 [-9.82243984e-04 -1.24698459e-01  1.54500583e-02 -2.36116868e-02
  -4.33256260e+00 -9.01258955e-01  2.95833770e-01 -4.52110548e-01
  -3.55329194e+01 -8.62810108e-02 -7.72472914e-01 -8.99132078e+00
  -1.92971420e+01]
 [-2.35608226e-01 -3.13830005e+00 -1.17389395e+00 -9.01200465e-03
  -8.80406399e-03 -1.29565593e+01 -1.41485068e+01 -8.83188572e-02
  -8.62810108e-02 -1.47653497e+01 -3.10434293e+01 -3.68548479e-01
  -8.32122150e+00]
 [-2.09923813e+00 -2.00748303e+01 -2.12548559e+00 -5.81850271e-02
  -5.68424809e-02 -6.57448692e+01 -2.99722078e+01 -7.90717729e-01
  -7.72472914e-01 -3.10434293e+01 -1.22003131e+02 -8.32123702e+00
  -6.20267447e+01]
 [-2.35611600e-01 -3.13833427e+00  4.86228914e-01 -7.64342556e-01
  -7.46706314e-01 -1.29566416e+01  5.88600845e+00 -9.20368420e+00
  -8.99132078e+00 -3.68548479e-01 -8.32123702e+00 -1.47653804e+01
  -3.10434873e+01]
 [-2.09924507e+00 -2.00748975e+01  8.27361978e-01 -1.40167890e+00
  -1.36933693e+00 -6.57450367e+01  1.17045986e+01 -1.97529156e+01
  -1.92971420e+01 -8.32122150e+00 -6.20267447e+01 -3.10434873e+01
  -1.22003284e+02]]
```
```python
import time, sys
from IPython.display import clear_output
def update_progress(progress):
    bar_length = 20
    if isinstance(progress, int):
        progress = float(progress)
    if not isinstance(progress, float):
        progress = 0
    if progress < 0:
        progress = 0
    if progress >= 1:
        progress = 1
    block = int(round(bar_length * progress))
    clear_output(wait = True)
    text = "Progress: [{0}] {1:.1f}%".format( "#" * block + "-" * (bar_length - block), progress * 100)
    print(text)

    
coulombRepulsionTensor = np.zeros((n,n,n,n))
count = 0
for i in range(n):
    for j in range(n):
        for k in range(n):
            for l in range(n):
                coulombRepulsionTensor[i][j][k][l] = twoelectron(aolist[i],aolist[j],aolist[k],aolist[l])
                count += 1
                update_progress(count / n**4)
                
update_progress(1)
#print(coulombRepulsionTensor)
```
```
Progress: [####################] 100.0%
```

Build Fock matrix

```python
from scipy.linalg import eigh
nelectrons = 1+1+8 #H H
norb = nelectrons // 2
maxiter = 100
C = np.eye(n) #coeffcient matrix
prev = float('inf')
tol = 1e-7
for i in range(maxiter):
    #start building Fock matrix
    JK = np.zeros((n,n))
    F = np.zeros((n,n)) 
    for j in range(n):
        for k in range(n):
            for l in range(n):
                for m in range(n):
                    for o in range(norb):
                        JK[j][k] += (C[l][o]*C[m][o] * 
                                    (2*coulombRepulsionTensor[j][k][l][m]-
                                     coulombRepulsionTensor[j][m][k][l]
                                    ))
    F = Tmatrix + coulombAttractionMatrix + JK #add kinetic integral and coulomb attraction integeral
    S = Smatrix #overlap matrix
    #now solve the FC=SCe (Hartree Fock Roothaan-Hall Equation)
    energy, C = eigh(F, S)
    EHF = 0
    for i in range(norb):
        EHF += 2*energy[i] - 1/2*JK[i][i]
    if abs(EHF - prev) < tol:
        print('SCF Converged')
        break
    delta = EHF - prev
    prev = EHF
    print('EHF:'+str(EHF)+" "+'prev:'+str(prev)+' '+'delta:'+str(delta))
```

```
EHF:-98.50144013664092 prev:-98.50144013664092 delta:-inf
EHF:-75.52313316329055 prev:-75.52313316329055 delta:22.97830697335037
EHF:-76.12168005535852 prev:-76.12168005535852 delta:-0.5985468920679722
EHF:-76.12473548753243 prev:-76.12473548753243 delta:-0.003055432173908912
EHF:-76.12635567733068 prev:-76.12635567733068 delta:-0.0016201897982455193
EHF:-76.12645217258822 prev:-76.12645217258822 delta:-9.649525753729904e-05
EHF:-76.12646225253813 prev:-76.12646225253813 delta:-1.0079949916530495e-05
EHF:-76.12646321190164 prev:-76.12646321190164 delta:-9.593635041937887e-07
EHF:-76.12646331382744 prev:-76.12646331382744 delta:-1.0192580646162241e-07
SCF Converged
```
