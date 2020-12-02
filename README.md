# Simple-classification-ML-exercise
Simple Machine learning algorithms for a fairly easy dataset
{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# <center> Customer Sales Activity Prediction using historic Data"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 65,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Part One Set Environment and Get libraries\n",
    "import numpy as np\n",
    "import matplotlib.pyplot as plt\n",
    "import pandas as pd\n",
    "import seaborn as sns\n",
    "\n",
    "from sklearn.metrics import confusion_matrix\n",
    "\n",
    "from sklearn.datasets import make_classification\n",
    "from sklearn.linear_model import LogisticRegression\n",
    "from sklearn.model_selection import train_test_split\n",
    "from sklearn.metrics import roc_curve\n",
    "from sklearn.metrics import roc_auc_score\n",
    "\n",
    "\n",
    "from sklearn.metrics import accuracy_score\n",
    "from sklearn.metrics import f1_score\n",
    "from sklearn.metrics import precision_score\n",
    "from sklearn.metrics import recall_score\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Data Importing and Basic EDA"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Part two Data Importing\n",
    "df = pd.read_csv('Sales_data.csv')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "RangeIndex: 400 entries, 0 to 399\n",
      "Data columns (total 5 columns):\n",
      " #   Column           Non-Null Count  Dtype \n",
      "---  ------           --------------  ----- \n",
      " 0   User ID          400 non-null    int64 \n",
      " 1   Gender           400 non-null    object\n",
      " 2   Age              400 non-null    int64 \n",
      " 3   EstimatedSalary  400 non-null    int64 \n",
      " 4   Purchased        400 non-null    int64 \n",
      "dtypes: int64(4), object(1)\n",
      "memory usage: 15.8+ KB\n",
      "None\n",
      "****************************************************************************************************\n",
      "             User ID  Gender         Age  EstimatedSalary   Purchased\n",
      "count   4.000000e+02     400  400.000000       400.000000  400.000000\n",
      "unique           NaN       2         NaN              NaN         NaN\n",
      "top              NaN  Female         NaN              NaN         NaN\n",
      "freq             NaN     204         NaN              NaN         NaN\n",
      "mean    1.569154e+07     NaN   37.655000     69742.500000    0.357500\n",
      "std     7.165832e+04     NaN   10.482877     34096.960282    0.479864\n",
      "min     1.556669e+07     NaN   18.000000     15000.000000    0.000000\n",
      "25%     1.562676e+07     NaN   29.750000     43000.000000    0.000000\n",
      "50%     1.569434e+07     NaN   37.000000     70000.000000    0.000000\n",
      "75%     1.575036e+07     NaN   46.000000     88000.000000    1.000000\n",
      "max     1.581524e+07     NaN   60.000000    150000.000000    1.000000\n",
      "****************************************************************************************************\n",
      "     User ID  Gender  Age  EstimatedSalary  Purchased\n",
      "0   15624510    Male   19            19000          0\n",
      "1   15810944    Male   35            20000          0\n",
      "2   15668575  Female   26            43000          0\n",
      "3   15603246  Female   27            57000          0\n",
      "4   15804002    Male   19            76000          0\n",
      "5   15728773    Male   27            58000          0\n",
      "6   15598044  Female   27            84000          0\n",
      "7   15694829  Female   32           150000          1\n",
      "8   15600575    Male   25            33000          0\n",
      "9   15727311  Female   35            65000          0\n",
      "10  15570769  Female   26            80000          0\n",
      "11  15606274  Female   26            52000          0\n",
      "12  15746139    Male   20            86000          0\n",
      "13  15704987    Male   32            18000          0\n",
      "14  15628972    Male   18            82000          0\n",
      "15  15697686    Male   29            80000          0\n",
      "16  15733883    Male   47            25000          1\n",
      "17  15617482    Male   45            26000          1\n",
      "18  15704583    Male   46            28000          1\n",
      "19  15621083  Female   48            29000          1\n",
      "****************************************************************************************************\n"
     ]
    }
   ],
   "source": [
    "#Basics EDA Data\n",
    "print(df.info())\n",
    "print(\"*\"*100)\n",
    "print(df.describe(include = 'all'))\n",
    "print(\"*\"*100)\n",
    "print(df.head(20))\n",
    "print(\"*\"*100)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "User ID            0\n",
       "Gender             0\n",
       "Age                0\n",
       "EstimatedSalary    0\n",
       "Purchased          0\n",
       "dtype: int64"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "#Data Na value count\n",
    "display(df.isnull().sum()) \n",
    "#No missing values"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Simple Data Visualizations"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    },
    {
     "data": {
      "text/plain": [
       "<matplotlib.axes._subplots.AxesSubplot at 0xfed5e07a20>"
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.hist(df['Age'])\n",
    "plt.title('Age Distributions')\n",
    "plt.show()\n",
    "df.Gender.value_counts().plot(kind='pie')\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<seaborn.axisgrid.FacetGrid at 0xfed559f940>"
      ]
     },
     "execution_count": 7,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432.875x360 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "sns.relplot(x=\"Age\", y=\"EstimatedSalary\",hue=\"Gender\", data=df)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "ax = sns.boxplot(x=\"Gender\", y=\"EstimatedSalary\", data=df) #hmm No biases here"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<matplotlib.axes._subplots.AxesSubplot at 0xfed5ef3b38>"
      ]
     },
     "execution_count": 9,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAZoAAAEGCAYAAABcolNbAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAADh0RVh0U29mdHdhcmUAbWF0cGxvdGxpYiB2ZXJzaW9uMy4xLjMsIGh0dHA6Ly9tYXRwbG90bGliLm9yZy+AADFEAAAgAElEQVR4nO29e3idVZX4/1lpmzS9kLZJejm9Slvwwvz8jnQELyC2w3WQeh2RjOJtmCmKzmBHwDLCSFvAQR11nDCICGgVvDC2ULDFYgVBEFDsvdAASdvTW07a0zRNc12/P/Y+yXtOTnJOk5ycS9bnefLknLXevd/17vO+e71777X3FlXFMAzDMDJFUbYNMAzDMAobczSGYRhGRjFHYxiGYWQUczSGYRhGRjFHYxiGYWSUkdk2IFeoqKjQOXPmZNsMwzCMvOLFF1+sV9XKvo4xR+OZM2cOL7zwQrbNMAzDyCtEpDbVMdZ1ZhiGYWQUczSGYRhGRjFHYxiGYWQUczSGYRhGRjFHYxiGYWQUczSGYRhGRjFHYwwLIpEIX/rSl2hoaMi2KTmFlYsxFJijMYaETFVo1dXVLF26lCuuuIIrrriC6urqpMetWrWKLVu2sGrVqkE9f75j5XJypHu/GfGYozGGhExXaM3NzTQ3NyfVRSIR1q9fj6qybt06e3v3WLn0n77uN6Mn5miMjJPJCm3JkiXccccdzJ07l7lz57JkyZIex6xatYrOzk4AOjs7T9rZFWr30kDLZTiSzv1m9MQcjZFxsl2hPfHEE7S3twPQ3t7Ohg0bTir9QFtjueqoBlouhpEu5miMjJPtCm3hwoWMHOmW9Rs5ciSLFi1KO+1gtMZydRxkIOViGCeDORoj42S7QquqqqKoyN3qRUVFVFVVpZ12MLrdcnUcZCDlYhgngzkaI+Nku0IrLy/nggsuQES48MILmTRpUtppB6PbLVfHQQZSLoWKRZVlBnM0RsbJhQqtqqqKM84446Sd3EBbY9nuNkxFf8ul0LGossHFHI0xJGS7QisvL+cb3/jGSTu5gbbG3vnOd8Z9f9e73nVS6TNNf8ulULGossxgG58ZQ0KsQss3Yq2xtWvX9miNVVdXU1NTQzgcBiAUCg155RSJRFi5ciXLli0rGGeRWK7nnHOOVfiDQHV1NU899RQw9PeqORrDSEFVVRW1tbW9tmb66mJ55pln4r4//fTT/Nu//dug2RaMaLvmmmsGLd9cIB+7rrJZmadDX2WaSdvN0RhGCnprjcUewqVLlwJwxx139Dhm4cKFPPbYY3R0dDBixIhBjbhLjGirqqoqiFZNYrnmUkWdDrnqIJcsWUJNTQ2Q/F6FzNlujsYwMkhVVRXr16/vcjSDOUaVLKKt0Fo1+UY6lXmukknbMxYMICL3iMhBEdmSRLdURFREKvx3EZHviMguEdkkIm8LHHuliLzi/64MyM8Ukc0+zXdERLx8kog87o9/XEQmZuoaDSMVmYy4y/WItkxRXV3dFX68dOnSHiHIqfTG0JPJqLN7gYsShSIyEzgfqAuILwbm+7+rgGp/7CTgJuAs4O3ATQHHUe2PjaWLnet6YIOqzgc2+O+GkTUyFXGX7Ymw2SRV+LGFJ+cWGes6U9UnRWROEtW3gC8DqwOyxcD9qqrAsyIyQUSmAecBj6tqA4CIPA5cJCIbgVNU9Q9efj/wfuAxn9d5Pt/7gI3AdYN4aYZxUmQq4i7WLQfDa2Z/qi6e/nYBxaLdgK7/sXGiXBvUzzeGdIxGRC4D9qrqX3xPV4zpwO7A9z1e1pd8TxI5wBRV3QegqvtEZHIf9lyFaxUxa9as/lySYaQkUyHIfYVeZ5pcj67qDzU1Nezcvospk2ZTpMUAHDnQxoGG2ixblv8MmaMRkTHAMuCCZOokMu2H/KRQ1buAuwAWLFhw0ukNIx0yGYKcKvQ6kxRi19SUSbP5h4tujJP9+NfLs2RN4TCULZq5wBuAWGtmBvAnEXk7rkUyM3DsDCDs5eclyDd6+YwkxwMcEJFpvjUzDTg46FdiGGmS6RDkbE2EzefoKmPoGbIlaFR1s6pOVtU5qjoH5yzepqr7gTXAJ3z02dlA1Hd/rQMuEJGJPgjgAmCd1zWKyNk+2uwTdI/5rAFi0WlXEj8WZPQDW2iw/+TyopqGMVRkMrz5p8AfgNNFZI+IfKaPwx8FXgV2Ad8HrgbwQQC3AM/7v6/FAgOAJcDdPk0NLhAA4DbgfBF5BRfddttgXtdwxiJ5Tp7hGoJsGEEyGXX2sRT6OYHPCnyul+PuAe5JIn8BOCOJPAIMnzjPISDfZ2pnk4ULF/LrX/+a9vb2YReCbBgxbGUAI+sUYgRTjOEagmwYQWybACMnKNRuuVzYi8cwso21aIysU+gRTP0NQS7kll4uEg6HaYwe7xHOfKChluMdY7JkVWFgjsYwMsxAQpCz1cobDCdXiHvlZJN83qfHHI1h5CjZbukN1Mnl2145oVCIIyPakk7YnDBlVJas6kk+djGbozEMowcDdXKFtldOOBzuirqE7KyFls/Rn+ZoDMPooq+FJSH9CrXQ9sppbm7m5e27mDbRrYk4otOthda4v5V9h+v6SmpgjsYwjAA1NTVs2rGTovLJdKpbUnDLocMAdEbSX80p2UTVfHY0ANMmzuKzi27sIb97g62FlgpzNIZhxFFUPpnR77uih/zEwz9JOw+bqHry5PNgfyrM0RiG0UU4HKbzaGNSp9IZOUi4Lb2BaJuo2n/ycbA/FeZoDMMYdDKxV85gjR/lKvk82J8KczSGYXQRCoVoGHW4166zUOXEJKmSM9h75dTU1LBtxy7KKmbRgRuM31vfCkC0Pr8H5At9d09zNIZhZIRM7JVTVjGLdy5e1kP+zOoVg3qeoaampoZd215h1viZFLe7OTutu09Q17g7Rcr8wByNYRhGDjBr/Ey+smBpnGzlC4WxJJMtqmkYhmFkFGvRGIZhZJlwOExTY1OPFkxt427GhsdmyarBw1o0hmEYRkaxFo1hGEaWCYVCtHacSDpGUxwanSWrBg9zNMaAKeQZzYZhDBxzNMagUYgzmg0jX8jljfIyNkYjIveIyEER2RKQ/aeI7BCRTSLyfyIyIaC7QUR2ichOEbkwIL/Iy3aJyPUB+RtE5DkReUVEHhSRYi8v8d93ef2cTF2j4ViyZAl33HEHc+fOzamb2zCGG7m6JXomWzT3Av8N3B+QPQ7coKrtInI7cANwnYi8GbgceAsQAn4jIqf5NN8Dzgf2AM+LyBpV3QbcDnxLVR8QkTuBzwDV/v9hVZ0nIpf74z6awes0jIKiM3KQEw//hM6oW7W5qGxil5yTWBnAGFqyvVFeX2TM0ajqk4mtCVVdH/j6LPBh/3kx8ICqtgCvicgu4O1et0tVXwUQkQeAxSKyHVgIxNbJuA+4GedoFvvPAL8A/ltERFV10C7OyAkKfdmObDB37tyuzzVHG5ws5lwqJ8bpDSNdsjlG82ngQf95Os7xxNjjZQC7E+RnAeXAEVVtT3L89Fga33KK+uPrEw0QkauAqwBmzZo1wMsxhpqamhq27NhESTm0+teIVw5toiWS+XMXqpML2h27nlx7Ozbyj6w4GhFZBrQDq2KiJIcpyceQtI/j+8qrp1D1LuAugAULFliLJw8pKYcZi+Nvkz2rOzN+XrdB2DakfDyx953Nh3ajkcaMn9sYfuT7i82QOxoRuRK4FFgU6M7aA8wMHDYDCPvPyeT1wAQRGelbNcHjY3ntEZGRQBnQkIlrKSRyOWIlV22T8vGMXPw3cbL21c9nyRqjkHGLbr7MrPHTKW531Xbr7ibqGvdm2bL0GFJHIyIXAdcB71HV4wHVGuAnIvJNXDDAfOCPuNbJfBF5A7AXFzBwhaqqiPwWN8bzAHAlsDqQ15XAH7z+CRufSY9cjFaJkcu2GcZQMGv8dL5y1ufiZCuf+16WrDk5MuZoROSnwHlAhYjsAW7CRZmVAI+LCMCzqvrPqrpVRH4GbMN1qX1OVTt8Pp8H1gEjgHtUdas/xXXAAyKyHPgz8AMv/wHwIx9Q0IBzTkYKcjlipTfbwuEwLUd7dpW1RCDcFsbITWyC7/Ajk1FnH0si/kESWez4FUCPTSVU9VHg0STyV+mOTAvKTwAfOSljDcMYcqyVOnywlQGMvCUUCtE0qj5pMECoMhQni0QirFy5kmXLlsVtK5wL4z+92VaoFPKWxUZybPVmY1iwatUqtmzZwqpVq3rosj2bui/bDKMQsBaNUfBEIhHWr1+PqrJu3Tqqqqq6Wg7ZHpuKRCKsW7cuqW25SL6H2RrZwVo0RsGzatUqOjtdwEBnZ2dOtRxWrVpFe7ubh9PW1pZTtiXDzR/ayZZDUZq0iCYtYsuhKJt27OxyPIaRiDkao+B54oknuirz9vZ2NmzYkGWLutmwYQOx6HtV5Te/+U2WLUpNUflUxlz2ScZ9/FrGffxaxlz2SYrKp2bbLCOHMUdjFDwLFy5k5EjXSzxy5EgWLVqUZYu6mTx5cp/fs0Wsi6ympoalS5dSXV2dbZOMPMYcjVHwVFVVUVTkbvWioiKqqqqybFE3Bw8e7PN7NiktLaW0tDTbZhgFgAUDGAVPeXk5F1xwAWvXruXCCy/MqcH2RYsWsXbtWlQVEeFv//Zvs20S4IIkbGDfGCysRWMMC6qqqjjjjDNyqjUDzq5gt16u2WcYg4G1aIxhQXl5Od/4xjeybUYPysvLufDCC1m7di0XXXTRSbe2httkT2PwGYqQdWvRGEaWGUhryyZ7GgPFrQy9k9a9DRR3CMUdQuveBnZtG7yQ9bRaNCJyKfCoqmZ+ow/DyCGGYoma/ra2+pqImgpb2DI75MKSR8mYVVbJje+K3/F++dMPdn0e6P2SbovmcuAVEfm6iLwp7dwNowDI9hI1vTEYE1Fz9doKmXwu8/7anlaLRlX/QUROAT4G/FBEFPgh8FNVtS0FjYIl20vU9EWyiajXXHNNWmltYcvskMv3U18M9H5Je4xGVY8Cv8RtNDYN+ADwJxFJ7842DGNQyeWJqIYRJN0xmsuATwFzgR8Bb1fVgyIyBtgOfDdzJhqGkYyqqirWr18P5N5EVCOeVJFdhU664c0fAr6lqk8Ghap6XEQ+PfhmGYaRilyeiGrEU1NTwyvbdjFz/CxGtRcDcGJ3K7sb67Js2dCQ0tGIyAhgeqKTiaGqubNCoWEMM6qqqqitrbXWTJYJh8NdLZTeWiwzx8/iy2d9JS7d159bmXb+TY3HWPnc9+LktY17GRseNyDbh4KUjkZVO0TkuIiUqWp0KIwyDCM9cnUi6nCjubmZl7fvYnrZLEZ2uBZLU7iVvdHh0WJJRbpdZyeAzSLyONAUE6rqFzJilWEYRp4xvWwW15y7LE723SdXDEreoVCI1o4mvnLW5+LkK5/7HsWhsYNyjkySrqNZ6//SRkTuAS4FDqrqGV42CXgQmAO8Dvy9qh4WEQG+DVwCHAc+qap/8mmuBG702S5X1fu8/EzgXqAUeBT4oqpqb+c4GduNwqevwVmw3SKNeFpaWtjXWsvdG5b30O07XEu7trmayEhKuvNo7utH3vcC/w3cH5BdD2xQ1dtE5Hr//TrgYmC+/zsLqAbO8k7jJmABoMCLIrLGO45q4CrgWZyjuQh4rI9zGEYXbqfILVAxGmgFYFP9LqesP5E9w7Dtko3CI93w5vnArcCbgdExuaqe2lsaVX1SROYkiBcD5/nP9wEbcU5gMXC/uq0GnxWRCSIyzR/7uKo2eDseBy4SkY3AKar6By+/H3g/ztH0dg7DiKdiNCPe/4Ye4o5fvZYFY7pxTnA7Uj6R2KpPmw/tRyPWMM8WJSUlTB47k88uurGH7u4NyznYtDsLVuUP6Xad/RDXsvgW8F7cnBrpx/mmqOo+AFXdJyKx7QSnA8Ffao+X9SXfk0Te1zl6ICJX4VpFzJo1qx+XYxiZQconMup9F8TJ2h5enyVrjHwmF+bwpLsyQKkPYxZVrVXVm4GFg2hHMqel/ZCfFKp6l6ouUNUFlZWVJ5vcMAwj5+lanXnPEYrbiyhuL6J1z5FBXZ05FWlHnYlIEW5hzc8De4H+bG5+QESm+ZbGNCC2b+0eYGbguBlA2MvPS5Bv9PIZSY7v6xxGjmFjEYYxNMw6ZQrL3vnxONmKZ340ZOdPt0XzL8AY4AvAmcDHgSv7cb41gXRXAqsD8k+I42wg6ru/1gEXiMhEEZkIXACs87pGETnbR6x9IiGvZOcwcoyamhq27djE7oObaNcm2rWJ3Qc3sW3HpiF70zIMI/OkG3X2vP94DDc+kxIR+SmuNVIhIntwYzy3AT8Tkc8AdcBH/OGP4kKbd+HCmz/lz9sgIrcAsfN/LRYYACyhO7z5Mf9HH+cwcpDxk+BvLo7vCX3+sZPuBTUMI4fp09GIyMP0Mfahqpf1oftYL6oeS8z6aLPPJTkWVb0HuCeJ/AXgjCTySLJzGIZhGNkhVYsmfzZMMAzDMHKSPh2Nqv5uqAwxDMMwCpOMTdg0DMMwDEg/6uyHuCVf2nETNu/HbYBmGIZhGH2S7jyaUlXdICKiqrXAzSLyFC6SzDCMJMTmCYXDborXOeecY3ODjLxjMFYWGOoJm4Yx7Ghubs62CYbRb9zKAjuYVVZBcYeTte6tpy5an3Ye6Tqa4ITNW3DLz/RnwqZhDBtirZfY25+1ZlLT19tzOByG4oqs2TacmVVWwY3vXhwnW/779OfCn/SETRG5Fjji574YhmEMGjU1NWzd8QqlFbNoxe1U+Wp9C831dYweVcTo4iwbaPSLVBM2vwr8TFV3iEgJ8GvgrUC7iFyhqr8ZCiONgVNdXc1TTz0FuN36bC0xI1cprZjF6Yuvj5PtXH0bGt3TSwoj10kVdfZRYKf/HOsqqwTeA6zMlFFGZmhubrbxAsMwhpxUXWetgS6yC4EHVLUD2C4i6Y7vGENAqginJUuWdPV533FH+gs+2ArLRpBwOEzn0UaOr7k3Tt4Z2U+4rSk7Rhk5Typn0SIiZwAHcPNnlgZ0YzJmVYEyFN1Xg91iqampYcf2TUyaCH6zRw7u30SDbfZo5CGJL2TWjTw0pHI0/wL8Atdd9i1VfQ1ARC4B/pxh2wqSTHVdZTLCadJE+LtF8b2sazd0Dlr+xuCTqTk8oVCIhlFRxlz2yTj58TX3EqosG3D+AyEcDnfd/6la30PdhRwOh2lqbOLrz8WPOOxurGVseCyhUGhI7RlqUq119izwxiTyR3FL+xsnQX+7rwyjvwynMbnm5ma279jFpPLZqLrwtAOH2miI1HYdk/hCZs/h0JAq6uzavvSq+s3BNSe7DPQt0CK7hp7mA1BzT3frSkaCtuPa4MOY4TqHZ1L5bC6+7MY42WNrlmfJmm5CoRAnOlr58llfiZN//bmVjA4Vfsx2qq6z8f7/6cDf4HavBHgf8GSmjMo2vb0FpuOIcu0NMtOD+dkMFpg7dy7hcDiuzEtLS7ucvGEYuUGqrrP/ABCR9cDbVLXRf78Z+HnGrRti0n0L7M2Z5GLXWE1NDTu3b2LyBBD/4n943yYOHhm8/Ldv38TEidDp89+/fxOHhyBYYMmSJcPmTd0w8pl0Q5RnAa2B763AnEG3JsfJ1+6IyRPg8vfG/9QP/LZ90PKfOBEWnR8v2/D4oGVvGEaek66j+RHwRxH5P9zWzh/AbRVgGCmxsSsjxmCsBGwMLuFwmKboUZY//WCcvDZ6kLFyYlAi4tJd62yFiDwGnONFn1JVC282+iQWbhoOh2loaABct2M4HO4e67Jpv0NONsfVampq2LzjFUaUz6BDRwGw7VAzHRFbXqaQOZnHfAxwVFV/KCKVIvKG2LyaQuDQoUNpx+An0teDm076dMjHvU2am5vZtn0TZZOg2O/LOnpcE0oT27bXU1I8luLxfedhDD41NTVs2rEDKa8ktu7H5kMRNHJoSM4/onwG4xfHB7Q2rk4dwNrS0kJLfS3PrF7RQxetr4XONsYNmpUnR0tLC3tbavnuk/G27T1SyzjSmydT17iblS/cwYHjBwGYMmYydY27mcd8r9/Lyue+x4Hj9V5fQV3jXuZx2oBsD4VCtOpobnzXR+Pky59+kOLQpAHlHSPdrZxvAhbgos9+CIwCfgy8qz8nFZF/BT6L64bbDHwKmAY8AEwC/gR8XFVb/WKe9wNnAhHgo6r6us/nBuAzQAfwBVVd5+UXAd8GRgB3q+ptqWxqaWlh1/btzC6bRHGHe/rawgeojTakvJ6amhp2bd/GrLJxFHe0AdAargOgLnosnSJJe8ZyrkW1paJsEpxzofSQP7VOOZFe0RgZQMorKXnfh+NkLQ//IkvWGMFuw9YaV4cUzxzNPOb36FJsrdnv9WOZx2l50eWYbovmA8Bf4xwAqhoWkX69i4rIdNy+Nm9W1WYR+RlwOXAJbvWBB0TkTpwDqfb/D6vqPBG5HLgd+KiIvNmnewsQAn4jIjHX/j3gfGAP8LyIrFHVbalsm102iRvPuTBOtvypdWld16yycSw75697yFc8dXI9jH1FtMHgByJYn7mRL5SUlDC6bCbvXLysh+6Z1Ss4Ed2dBascJSUlTC2dyTXnxtv23SdXMDaNeTLB5znVZNJ8nGyarqNpVVUVEQUQkbGDcN5SEWnDdcntw22mdoXX3wfcjHM0i/1ncMvh/LeIiJc/oKotwGsisgt4uz9ul6q+6m19wB+b0tFkk0zNWA6HwzRGe0aZHTwCzepaTzu2b6JiAuDDk+v3baJ+kMKf+6KlpYWWVnj+sfitjRojEG4PZ96ADJLOOEg+docaRn9I19H8TET+F5ggIv8IfBq4uz8nVNW9InIHUAc0A+uBF3GbqcVqwz3AdP95OrDbp20XkShQ7uXPBrIOptmdID8rmS0ichVwFcD48cN3sKBiAnxw0Yg42UMbOrJkTWHgxkG2IeVluAXPYfOhvWgk2uPYfOsOTQeLNDSCpBt1doeInA8cxY3TfFVV+zVTQkQm4loYbwCO4CZ+XpzstLEkveh6kyfbYyfpbqCqehdwF8DUqVMLcsfQUCjEYalPOo9m4rTsLuRXUlJC8fh2/ubi+J/y+ceU0OT8X2RQyssYufjdcbL21b/v+pyv87LSJR8d6IGGWn786+UcbnTjIBPHT+VAQy0ji5NVN0a6pBsMcLuqXgc8nkR2svwt8JqqHvL5PAS8E9daGulbNTOAWN/JHmAmsMfvgVMGNATkMYJpepMbRsGQy62GXFwlIxXBMcnIMTc/fcKUUUyYMq+re9PoH+l2nZ0PJDqVi5PI0qEOOFtExuC6zhYBLwC/BT6Mizy7Eljtj1/jv//B65/w40VrgJ+IyDdxwQDzgT/iWjrzReQNwF5cwEBs7McwCop8bDXkKn0NyC9dupTG/a1J0xmpSbV68xLgauBUEdkUUI0Hnu7PCVX1ORH5BS6CrR23r81dwFrgARFZ7mU/8El+APzID/Y34BwHqrrVR6xt8/l8zu/+iYh8HliHC2++R1W39sdWw8hl8rHVYAxPUrVofgI8BtwKXB+QN6pq6gkmvaCqNwE3JYhfpTtqLHjsCeAjveSzAugxeyvX9ss5mQ2ZDMMwCo1UqzdHgSjwMQARmQyMBsaJyDhVrcu8iflPc3Mzr2zbwqyyEoo7XPO7Ze8r1EVbsmxZbpPNpVJSvRxkknA4jB6N0vbw+ji5Rg4TbuvMarkYRn9INxjgfUBsLOQgMBvYjpssaaTBrLISrn/3rDjZbb83P90XNTU1bN2xibGToM3HBL5+cBNN/W5Lp09zczObdmyFirGAm6m9qf51qG9KK33MWQSjzAA0EiXcNrAIpu4lZCahfg2ZzYcOopEhKBjD6AfpBgMsB84GfqOqfy0i78W3cgy/+umRxqSrANQeaaQNgdGjsmBZ/jN2Erzl0viI9a2PdPZy9CBTMZaRi/8qTtS+enPGTxsKhYiMKmLU+y6Ik7c9vJ5Q5VQApHwSo953SYI+vd5i5wSP9lhyRiOHCLdZKzvfSLXWYjgcpoIxWbEtRrqOpk1VIyJSJCJFqvpbEbk9o5blGH39mMeOHUs6qccYvjhnoUnn0YQq83+OkJE71NTUsGvby8w6ZRrF7W7ideueRgDqju5DSkZASX44miMiMg63ffMqETmIi/QaNriFM7czu6yM4g4307stHKY2GmVEcTEzx43uda2z3c1tdK3vYhgZJtUYjnOCJUkX1QxVlg+tsQXE3mgd331yBfXHDgBQMW4Ke6N1nBaal/FzzzplGsvO/mwP+Ypn72Z3y8GMnz8V6TqaxcAJ4F+BKtykya9lyqhcZXZZGTee+5442fInf0edzWUwcojuMZzywBjOITQSybJlhUtpaSmhua6lur/GBfyMDRVzWmgec+fO7XL4w5V0l6BpAhCRU4CHM2qRYRQ4wYg26NnqCIfDMCrZSkrpI+XlFL9vcZys9eHVvRxtDJRQKBQ3uRPi5zYFf++hpqWlhdqWA6x45kdx8tqjBxgbPj4oO2imIt2os3/CtWCacX1Agls/7NTMmVY4tLS0UNfS2SPKrC7awlixpS2GGy6ibTtSPgEAVdetuvnQPjRyhLGjimHUQBdIN4zcId2us6XAW1S1PpPGGMZwQconMPKy9/aQt6/5LRw9ngWL8p+WlhZaI7U8tmZ5nLwhUktHW3YHw7NJSUkJM0vKWfbOj8fJVzzzI4pDE4bEhnQdTQ1gd38/KSkpYcbozqTzaEqGoNmaScLhMNEobEhYy/vwYVBtYXS29tale9HJ4HpgpaWlhEIh1z2Vej+qXnEhwo20r34+Tq6RRsJt4SHpjjCMfCFdR3MD8IyIPAd0Bdqr6hcyYlUWaGtro/ZIQ48dNWuPNDCGDqs4+kFHRwfRBrdtcyLRBkBbKO5jG6BwOExTtOe8maY0N0arqamhvqE+7i5voon6HfWMHTV2QI7GyD1KSkoYd8osLr7sxjj5Y2uWM6XS5rFlk3Qdzf8CTwCbsThdI0AoFKKoqJ5F58fLNzwODQ0jyHoU/NQiRnxwdJyo46ETbmGlAeBChDsYufhv4uTtq5+3eTKGkUC6jqZdVa/NqCVZZtSoUcyeMIkbz7kwTr78qXWMCk3JklX5TUlJCaPHtXPOhT2nsz61TjlxrIS+HFEoFKJ1ZH3Slfa3LXUAACAASURBVAEKYWO0TBGb+Z8YZaaRCOG2NmudG0NOuo7mt37b44eJ7zqzxZUMIwNo5DBtD69Ho26Gt5SNRyOHwS9Bk6+Ew2HajzbRuPqbcfL2yB7CbWPNCRYo6Tqa2MZhNwRkFt48RNhqvcOL0tLSrhWia466RTznVk6FyqlpTf5z3Xqjks6jCVVWZsZow+iDdCdsviHThhi9U1NTw8vbNxEqE0Z0uIH1Y+HNhKM9B9mN/CfV5L8rrrjCbyMQv4im6xprT6tVoJFDtDz8CzR6BAApm4BGDkGGl6AJhUIcGdXM+MXxPfGNq79JqLI0o+c2skeqHTYXquoTIvLBZHpVfSgzZhmJhMqEfzo3Pkzqf5+0rWWNkye4n07NUedo5laWQ2V5WnvtdEb2c3zNvXRGXc95UdkkOiP7obIsMwYbeU+qFs17cNFm70uiU8AcjWEMMa5rbGTSbQJClZNTpg92tSZrMfVFvJNy87fnVpZBZVnGN4Qz8pdUO2zGtlv+mqq+FtSJiHWnDRKpxmAMI1cYiJMyhi/pBgP8EnhbguwXwJmDa87wpKamhle2bWJGWRGjOtw0pea9W9gTtSlLRn5hL01GMlKN0bwRt11zWcI4zSnA6OSpUiMiE4C7gTNwXXCfBnYCDwJzgNeBv1fVwyIiwLeBS3DL4HxSVf/k87kSiE0DXq6q93n5mcC9QCnwKPBFja2XnqPMKCviX99VEif71tO226GRX7gtCl5mRHmITnWbcG09dIyOiC0eO5xJ1aI5HbgUmED8OE0j8I8DOO+3gV+r6odFpBgYA3wF2KCqt4nI9cD1wHXAxcB8/3cWUA2cJSKTgJuABThn9aKIrFHVw/6Yq4BncY7mIuCxAdhrGCeNRqK0r/49GnUhylI2Fo1EaekEjbS6BTR7pDlCuC2n34lSMqI8xNjL/jlO1rTmzixZY+QCqcZoVgOrReQdqvqHwTih39PmXOCT/hytQKuILAbO84fdB2zEOZrFwP2+RfKsiEwQkWn+2Mdjk0ZF5HHgIhHZCJwSs1dE7gfejzkaYwiJHzR3XUhzK6dD5XRefvll2js7smWakafEuiVjXZLV1dUsWbLErQl49Bgrnr27R5rao/toox1KeqiGlHTHaD4gIltx+9H8Gngr8C+q+uN+nPNU4BDwQxF5K/Ai8EVgiqruA1DVfSISC5+ZDuwOpN/jZX3J9ySR98CvdnAVwPjxfazuaAw7WlpaoF5pX705XlHfRLg19erMfQ2aL126lM2H9vW6TUCoctoArTdykd2NdXz9uZUcPO62ep48Zgq7G+uYz8lt9VxaOvjzjeqih1j+9IMcaHLh7lPGTqAueoh50ycNSv7pOpoLVPXLIvIBXMX9EeC3QH8czUhcYME1qvqciHwb103WGz0XynJdZScr7ylUvQu4C2Dq1Kn53V9hGEbOEmzhtvmtnkfPLGY+89IOkuhtBZBQKERrZyPLzv5sD92KZ+9md8vBtG1rrTkMQPH0ScybPmnQtqFO19HE1ti+BPipqja4Mfp+sQfYo6rP+e+/wDmaAyIyzbdmpgEHA8fPDKSfAYS9/LwE+UYvn5Hk+IIknW2BS/v9U3XvN7N2Q3wEXOQwtHcW7r4rJSUltJeNYOTiv4qTt6/eTKgi+9eskQbaHn4UjR4FQMpOQSMNkMY8GmPoyeWw8FS2DcY21Ok6modFZAeu6+xqEakETvTnhKq6X0R2i8jpqroTWARs839XArf5/7GlZ9cAnxeRB3DBAFHvjNYBK0Vkoj/uAuAG7wQbReRs4DngE8B3+2NrPtDc3MzO7ZuYWua8SZFfoiYa3sz+qDKyeCylw3dzwV5xXWOddPzqtZ7K+hO0dBbhYlRyj/jxH7fo5tzKyVA5edDeQA1jMEl3rbPrReR24KiqdojIcdwgfX+5BljlI85eBT4FFAE/E5HPAHW47jlwUWOXALtw4c2f8jY1iMgtQGyLw68FVpNeQnd482MUeCDA1DLhk+f13Njp3o1t1DcnSXAShEIhRhbV83eL4pfqX7uhk8lTs/9mPxwZijdQwxhMUs2j+bKqft1//VtV/TmAqjaJyDJcSPJJo6ov4cKSE1mU5FgFPtdLPvcA9ySRv4Cbo2MYSXFdY8qI9/dc4KLjV69REpVsb9lmGAVDqhbN5UDM0dwA/Dygu4h+OprhSF20hWvX7eJEe3fMQWtHJ2+ZHtuyuLPHBM090U7GihteOnZEeyyiGT6itHQcZ3+Lcu/Gth7n3H9E6aAlV3uAAGhsgOcfU467oQbGnOJkFPhQg0aOdM2j0egxAKRsHBo5AhZ1lhFsu43skcrRSC+fk303eqG0tJTQ3LmEw2GKmrv7sib6fUfC4YKNVeiTuH1XGt2DP3PyXJhMQY81JEYZdc+zmQaV02yplgxRU1PDjh27qJw0G9SthB452MahhtosW1b4pHI02svnZN+NXgjuL5KMmpoamrUh6RI0pT6q6xiRpNsEHGgeQ0Xp8T7GaPreLjkdGg67MRk/7swp451s8gA3e0y170qhjjUkvjnnWhRSIVM5aTYfuvTf42S/fOSWLFkzfEjlaN4qIkdxrZdS/xn/vd9rnRn5Q/DturHJvXlPnjqXyVMLu9WRDhpppH3182j0OABSNgaNNEIebGLZ2yxzw8gEqZagGTFUhhi5SToRTocPw4bHodG3eMaPh8OHoTi+AVZQJF9iZiZU5tcqxZmYZW4YiaQ7j2ZYUBttYPlT69h/zNWYU8eNpzbawLzQlCxblrsEK9Um3+KZOnUuU6e6IAelKVumZZRcnoAHblvn1odXo9EoAFJWhkYiUOmaW9Z6MYYSczSekpIS5r3pTQC01rgooFGhKcwLTRn2XUR9kWpNr70H6rNiFzhHx9FOOh5KmFtc30lLZwtQmE2u+NaW6+2eW1kJlZV51drKNfYdruPuDcsBiDS69crKx09h3+E6xk89ufXKhhvmaDyVlZXDcmDaKDxyvbWVjyQ66INNbqrB+KnFjJ+a/nplwxVzNEbBEgqFqC9uYMQH4+NWOh46QUm0hPYsB07agHz+YJGCA8McjWFkGRuQNwodczSGkSWs9WIMF8zRDHPC4TBHo/DQhvgdH+uPQKsOzxULjOwRDoc5frSJnatvi5Mfr6+jqLPNJu/lKUWpDzEMIxcJjvEsXbqU6urqbJtkGEmxFs0wJxQKUSz1fHBR/NzchzZ0UDFtmG8DUN/ktnKO+vDostFQ3wQV2TUrSKGN74RCIU4Ut3D64vhNd3euvg2N7ukllZHrmKMxcpqmBtj6SCcn/OJHo09xskyv7hy34GfUz/yvmAMVuTPzf8mSJTbOY2Qct7r8UZb/fnWcvDZaz1hp7SVVPOZojJwlbuKhX915TmB150ySasFPwzDSxxxNgEKd13DwCDzw23YOuwUPmDjOySYOwbYn0QZ4ap3S5NdBGzu+Wz49xco+NvHQMLJPKBSiVYu58d3xmyov//1qikPp9SObo0lCIfV7x/bCAWjwDnTitLlMnJb51ZfjWiTH3HmmT5nr/+dOF5RhGJnFHE2AQmi9JJLNPV9yvkVSf4KOX70GUd/PXFbcJc+lAX/DyHfM0QwSddFjrHjqzxw45vYmmTJuTJd83jAP3spFkg/2+xZWjgz4B7ceXrp0qW03bGSNumg9y3+/mgNNbjXwKWPLqIvWM296jnedicgI4AVgr6peKiJvAB4AJgF/Aj6uqq0iUgLcD5wJRICPqurrPo8bgM8AHcAXVHWdl18EfBsYAdytqvGzvwaZYPdUq68YikOzAJgXyo1Ky4gnXwb7C6kb18hPgvVXa41zNMXTK5g3vSLtui2bLZovAtuBU/z324FvqeoDInInzoFU+/+HVXWeiFzuj/uoiLwZuBx4CxACfiMip/m8vgecD+wBnheRNaq6LVMXki+V1rCk3m8TEO1038uKoL4zL7rGLHzZyAVSdYFfffXVKfPIiqMRkRnA3wErgGtFRICFwBX+kPuAm3GOZrH/DPAL4L/98YuBB1S1BXhNRHYBb/fH7VLVV/25HvDHZszRGLlJXDBCsHssR7rGjKHFLbd0nF8+ckuc/FCklpb2MVmyamioO3qAFc/8iANNhwGYMnYidUcPMI8JQ3L+bLVo/gv4MuCDXSkHjqhqu/++B5juP08HdgOoaruIRP3x04FnA3kG0+xOkJ+VzAgRuQq4CmDWrFkDuBwjF8n5YATDGAJc174bKG6tiQBQPGMC85gwZC9cQ+5oRORS4KCqvigi58XESQ7VFLre5MnWb0u68Yiq3gXcBbBgwYLsbk5iGHlEPgYqhEIhSka28aFL/z1O/stHbqF88qgsWdVNpubx5ULXfjZaNO8CLhORS4DRuDGa/wImiMhI36qZAcSWDt4DzAT2iMhIoAxoCMhjBNP0JjcMY5CwQIXMUIjlOuSORlVvAG4A8C2apapaJSI/Bz6Mizy7EogtrLPGf/+D1z+hqioia4CfiMg3ccEA84E/4lo6830U215cwEBs7McwjEHAAhUGn0Iuz1yaR3Md8ICILAf+DPzAy38A/MgP9jfgHAequlVEfoYb5G8HPqeqHQAi8nlgHS68+R5V3TqkV2IYRlZoiNTy2JrlNEb3AzC+bCoNkVqmVM7LsmXDm6w6GlXdCGz0n1+lO2oseMwJ4CO9pF+Bi1xLlD8KPDqIphqGkeO4Sbhu0PvYUbfaw5TKUUypnJfx5ZaMvsmlFo1hGEa/yeZyS0bf2A6bhmEYRkaxFo1hGMYgkY9h3+kw0NBrczSGYeQN0fo6nlm9gqboAQDGlk3pkk+vyI0B/0IMT47R32szR5MnhKPK/z7ZSv0xN6+0YpwQjiojirNs2DAm8S0v8Q22UDfSyxbBwf4av7XD9Ipi/39eTiwrlK2w77qj+1jx7N0caHIz/6eMLe+Sz+tagKX/DPSazNHkAcEH6ICvtMaF5nJayK3fBMezZJljuFeoqd7yCvkNN5FwOEzH0WM0rbkzTt4RCRNuG0co1P89MwZjhvuhhlp++cgtHDnqwp8nnDKVQw21lE/OjdZQf4hfXfkgAMUznHOZx/iccMDmaPKAvtbsWrp0KdFwJCt2JTKcKlRI/ZY3nJztydAR2UPj6m/SET0EwIiySjoie6ByPgDN9XXsXH0bLVFXaZaUTaa5vg4q5g/ovMEK90ijaxGVTx5F+eTcaA31l3xY088cjTFgrEI1YoRCIQ6POsbYy/45Tt605k5ClePiV9Q+2gbA3MpSqJzfo7KPdY+dWlECFT31J0s+VMiFijkawyhAcrU782Qqe3MGhYM5GsMoYAqxO7NQQ4izTSbL1RyNYRQghV7xFqIDzQUyVa7maIyMk8030FztQjL6T6oQYmvx9I9Mhmabo8kR9kQ7+dbTLRxqcnvbV44tYk+0k/nTUyQE9keVeze6gdUGP89m0jhhf1Qp63806aCS7TfQbJ/fGFrs984tzNHkAMFomjb/JlY6fS7zp6fe2z5RX+/Tl4XmUhZKnR6g/gg8tKGD6DH3vWyck1VMO5mr6J1s7l1ib7LDD9srJ/cwR5Mm4XCY40eOsPzJ38XJa48cYcwA8x5I2GXiA3Wy6YOOKOqdVMW0uVRMS89JGYaRPpns1svlLkNzNMOc4T63IJcfTqMwyWS3Xq52GZqjSZNQKEQbcOO574mTL3/yd4wawLIaRvbJ1YfTKDwy2a2Xy12G5miMYU0uP5z5SkckTNOaO+mM1gNQVFZBRyQMladl2TIjW5ijOQlqo1GWP/k79h9zo+ZTx42jNhplnm/RWDeMMdyJX2LGLeU/t3IcVJ6WF2N+hRwOn836yRxNmsSvkOp+rFGhEPNCoTiddcMYw5lCGfMr1Oc4W9c15I5GRGYC9wNTgU7gLlX9tohMAh4E5gCvA3+vqodFRIBvA5fg1sP/pKr+yed1JXCjz3q5qt7n5WcC9wKlwKPAF1VVB2J3Og9QLnfDWGvLGEr6ut9yudWQK3ZkgmzWT0VZOGc78CVVfRNwNvA5EXkzcD2wQVXnAxv8d4CLgfn+7yqgGsA7ppuAs4C3AzeJyESfptofG0t30RBcV85TWlpasG9qRu6R6n7Lx/uxurqapUuXdjnK6urqbJuUFwx5i0ZV9wH7/OdGEdkOTAcWA+f5w+4DNgLXefn9vkXyrIhMEJFp/tjHVbUBQEQeBy4SkY3AKar6By+/H3g/8NhQXF+uksutLaPw6Ot+K4T7MN8cZLbJ6hiNiMwB/hp4DpjinRCquk9EJvvDpgO7A8n2eFlf8j1J5HlNLnc35AK7du1i6dKlfPOb3+TUU08dtHyty3HoyeS9nmr77VTYb98/stF1BoCIjAN+CfyLqh7t69AkMu2HPJkNV4nICyLywqFDh1KZnBPN5t66G3LBtmxy++23c/z4cW699dZBzzsfu3gKgUyWu/2mQ0tWWjQiMgrnZFap6kNefEBEpvnWzDTgoJfvAWYGks8Awl5+XoJ8o5fPSHJ8D1T1LuAugAULFqQdLDCQGzQSibBy5UqWLVvGpEmT0k6X7pvUcHx4du3aRW1tLQC1tbW8+uqrg9aqsS7HoSeT5W2/ZXbIRtSZAD8AtqvqNwOqNcCVwG3+/+qA/PMi8gBu4D/qndE6YGUgAOAC4AZVbRCRRhE5G9cl9wngu4Nh+2DcpKtWrWLLli2sWrWKa665pktuTfr+c/vtt8d9v/XWW/n+97+fJWsMw0gkG11n7wI+DiwUkZf83yU4B3O+iLwCnO+/gwtPfhXYBXwfuBrABwHcAjzv/74WCwwAlgB3+zQ15EggQCQSYf369agq69ato6Ghoccxudikz/VuuVhrprfvhmFkl2xEnf2e5OMoAIuSHK/A53rJ6x7gniTyF4AzBmBmRli1ahWdnW6/mc7OzrhWTT60SHLNAcaYPXt2nHOZPXt2Fq0xDCMRWxlgCHniiSdob28HoL29nQ0bNsR1n+Uque4Er7vuOq6++uqu7zfccEMWrTEMI5GsRZ0NRxYuXMjIkc63jxw5kkWLejTgskZwjGjp0qVD2j2W6typ9PPmzetqxcyePXtQw5sNwxg45miGkKqqKoqKXJEXFRVRVVWVZYviyeb40EBnkV933XWMGTPGWjOGkYPIAJcAKxgWLFigL7zwQsbP853vfIe1a9dy6aWX5kW3mWEYRl+IyIuquqCvY2yMZoipqqqitrY251ozhmEYmcIczRBTXl7ON77xjWybYRiGMWTYGI1hGIaRUczRGIZhGBnFHI1hGIaRUczRGIZhGBnFHI1hGIaRUczRGIZhGBnFJmx6ROQQEFz2twKo7yNJX/qBpM20frie22zLv3Obbflx7tmqWtnH8aCq9pfkD3ihv/qBpM20frie22zLv3Obbfl37t7+rOvMMAzDyCjmaAzDMIyMYo6md+4agH4gaTOtH67nTqU323Lv3Kn0ZlvunTspFgxgGIZhZBRr0RiGYRgZxRyNYRiGkVlONkytEP+Ae4CDwJaA7P8BzwIRoA2oCejeCvwB2Aw8DjwJbAe2Al/0x0zyuteABmBngv4jXqb+mKDuP4EdPs9DSdLeAmzysgbglaA+YOctPv+XE9LfDOz1skagLjE98O/AcaAFFzMfS/sg8JI/vhk4kZB3rNy2AkeTXFus7LYAh30ZbgX+w+vfAPzR53sE2BbQfR7Y5a/pT8BfEtKuCpTVoST6H3jZZl9ucecOXPv/AB1J0t8LvA40+WuvCegEWOF/i+ZA+cb0T/ly+wvQCkQT9IsC19RI9/0U0y/0+i3A/cCfgUcCZfacP/eDwOgEfbDcKoARCfpYuW3BPQslCfpYuW0CfuE/P5JQZt8FjiXJ+15/D7zk/96WoI+V28v+mr+YoH8qkDYM/CpBHyu3l4Df+zJ7JEmZHfO/90v48Fy6n9HYb7Y1Qf8RL+v0505MH3tON+HuicT0sef0JZ//9qA+UHYN/rfZHEh7M+4eegl3v7yemBa4xv9urbj7PXju2HP6EtDuzx/Ux57Tl3DP+K4Efew53eqvPfb7vCOh3B4HJqasY7NdyefCH3CufwCCjmY9cLHXXQM0BXTPA+/xn/8V+L7/PN7/IG8Gvg5cD0wDvgPcnqB/E/Bu4AVgQYLuAtxeQdNwD2pi2lP8+ab589wZ1HvdTOC3wD5c5RJMfzOw1Kd/WxLb34tznmcFKrKuvAPn/hHw1YS0sXKb5sttY4L+eeA9uApmCe5hHIWrKM8GfgZcDozz1/W5gO6vgTm4h262tyOY9hKfrwA/9/kH9bFyE1zFeH1Q73UL/HUdS5L/vcCHgXFJdJ/COYAib/vkxLwD5/4V8ImE9C/j7gnB3VP3BvTvBHYDpwXuzWfprlB/BlzuP9+JcwY/CeiD5VYBXJugD5bbT5OkPyVg/0ZchfRIQNZVZknyvhf4cODYRH1XufnvXw3qE57TX3pdMP3LwJv8518ArwKP+N8hWGZH6Pki9nXgev/5MPCdBP2bgNP9NYeBigT9BcBI/zmaJH2w3CLAvUmuaSbOCewO5o9/Rv3n15Oc+73Ab3AvBa8Db+yjfosCtyXI1gMX+88HgKcT9LHn9D7gh7jntBiYkFBu1wO3p6pjresMUNUncW8VcWLcjfKk/94W0J2Oq4jB3dzv8vnE3kSnA4uB+1R1H3Ar8P6gXlW3q+rvcQ8nCbr1qtru064GZiToj/o0+3APiCacG+BbwL/g3lYSbYtd9z5V/VMS/RLga6r6nNe9lpgW2A+cB/w0IW2s3PbhWkLhBP3pwJPq7tJHgA/hKtRRPu1C4Beqegx3k78/plPVP6vq6/78Tf7/qID+UfUAzwAzEvRHA/aP9Ofr0ovICNxb6pcDxwVti5XbsSS6WJl1quoxVT2YLC3OCb0H52yC+li5Ka5FEg7oO4AWVX1ZRGbg3igFQEQkVmY+/0dxldDdAXuD5TYN+LsEfbDcdgJnJuiP+nPNAObhWpx4WbDMJDHvID59or6r3Lz+PcnSi8h4XOsllKBX4BSf9v/h3sIBymNl5r83A5cmZLsYd4+Bew4vCSr9M7oz2bV4/XpVbfdfW7xtQX3wfisi/j6I8S26WzQnwxKc82jx35PO5Pf3x1jgoQSVAqcEbNufoD8d13I8F+f8P6Sqrap6hPhyiz2jfZPKEw2XP9wbX7BF8yZcl9Ju/yPsDOieARb7z9cCjYE86vwPeCQh/8NBfUC+EfdG2EPn9Q8D/5Cox3U37MZ1C1QmnPsy4NsaeBtK0N/s5ZtwXSUTE/QvAf+Be5v+nc8v0e5z6W5mB9MGy20vMDtBHyy7L+Eq0WO4VlsFsMvrRuCa7R0kvDF52yd7O48l0Y/CdZm8kqjHvZ0dwLX2/hLU47pt/tV/PpaYP+7tfKcvt4MJugiwDNdCfQzX5ZfMtk/gnEJi3uf4PPb4tJsC5SK45ZEW+LQ/pfvNvavMfD5rcd0g59Gze+t1YA3OkSTTj8Ldp59J1PtyO4F7072Q7hZFsMzaE/NOKLOXca23oD5YbvtxlVYy2z7hyyYx/1i5Hfdlcokvl64y88dFcc7mReAqLzsSyP81b3+XPuEZ3Yu7p3ro/THHfR5xerqf01ZfBsHzXwZ826dr8fdETHcz3c9oI+5eDaYNPqPNuC68ZLaf6/P+U0L64HPajqtHgvpncC+qf/R/HTgHP5YkdVvK+jXbFXyu/NHT0XwH58XBdd8cC+jeiGt6vgjc5G/0cf77BxNv4tiPEdQn3MTn9KJbBvxfYt4Jx9wArIzpgTH+5ivz+tdxlX3Qtim4irzIPwj3J+i3+OsX3BtmSxLbqnGOIvG6g+X297gKPahPVnYT/HHnEF9pzsQ5m98CZwTkr+O7EgJpg/rvA//Vh34EbhzmUwH9ubg+/lhXyLHE9LjWgOC6K+7z5R7THQO+5NN8EDe2kOzcjwXKJ5j3Q3R3Vf4b7qEO6t/hy+IAsBznRB/BvWTEnPOl/rfcTPLK+gBwj/+cTP9r4C/J9D7val9ut/lzh2Jl5vVtiWkDZfZ+XGX41QT9Mdx9dKkvm6d6se0F4NdJ8n8I97L3P77c1gZ07/D5/RH4L9wb+mRcpX0u8Y4mhHtGu/QJz+hF/nMy/TJvuyTT+2NW4hxD8PzPAWX+3K/jno2YLviMfhv3QhhMG3xGL8E5q2S2VdM9zhdMH3xO/wnXDRfUvxHXPau4eTMRb8ctmKMZVEcTpXue0Rygo5d0p+He8tYB1wbkO4Fp/vNMXFfPtUnS/w7X3L82QX6ll5+SmHfCcXNxbzzX+u9/hXvbft3/xQYCb+ol/bxgei/7Ne5hHuXPXQ9UBvQjcZXWnCTXHSy3Uf78vdl+GvBH//kmXEVRT3dl/w6f/034/movf534/uwuvf/8K3yff6I+IHsP3RXSTf5vf6DcOumuwJOlPw9X2d6EG+/aAczxOgGiSWwrxz2woxNs+zfig01mAduSpL8V91a/3/+mx3ED+fX+N7nV//bN/pjjwI8D+R7Bdcm9nqj353nZ559MHzv3PlzL5jiuYo6V2RFcpdTWy7lvxQ1YHw/qY+UWyL8zybnL/TUl2rYWF5ARSxt7O487t8/jAuBn/vPN/jcLPqPT8L0WBMZH/PeN+JZRop7u53RMMn1ANhtfv3j9v9PzOa0D7kiSdk5C2qX4ZzRwTA3upSNoW+w5nZFoG/HPqQBHk1zbVG/baThnfY4v86Tl1mf9OpDKuZD+6Olotsd+SOAKoDmgm+z/F+HeIJ/Gv0EHjvlP3ECZ4JqtPRai87r9wE8S5Bfhuk8qff6Jec8PpP8j8Eov1yS4N8Y7E+TTAvrnE28U4J+Br/lz34t7gCXBvt/1Ytt2XCUsuJbLgQR9rOwm47qAPg2U4t48L8UN4v8j7m3+TlzXzFPApYE86oBT/edg2s/imvwzgQkJ+vcB87ysEhcMcEcwfSD/SrpbNMH8p3ndBNwb8h0B3W3+Wir99+cT8/bl+kAS2y7FOYvTfPrP4wa+g/pYuZUABPpsDAAABCFJREFUG3ABAzFH+XPigwGupveus1hL8LxA+li5lQaOPY/uLqhYuYm/5gcT8/b6Y0nyDt5r/+XLKai/Dfh0IN3zibb7crsviW0jY+Xm5Z/B3ZexvGNlNhHnLBbiun6ewd3DsWd0LK6l9fWgPnC+J/GthIT0sed0NjA+iX5+QLYU1+0Zl7//Pt7/NrMCaacF9Nfj7ptg3rFndCwu2GN3krwvwt0/yWyLPadjcffXiwn6WNk9hXtx+zTOCf1nrNy8/nrg6ynr12xW7rnyh6vw9uHexvb4G/bdvvAP4/pXg7ov4t7+Xsa9lSndYYwv4Zqy5bgKYbfXb03QfwD3RhN7Czwa0O3y6V7x+khC2l/ims6xkNVtQX3gut7dy7l/hOteSZoeF12yzuua6Q59vMTne6+/2ZJdd6zcYnm/kqCPld3ruLetTf5avurzPtXb1oJ7S94a0H3B/wbtvswaEtK2497sdtD95rwFV4kU4V4INnubYmm70gfK7f/D9Ukn2vYE3aGwh325xXQTcG97r+Bary8n5o2r7JbgunAS8/6At20nroW5I0H/n7jKYSeu7/w8uivUU3EvHLtwTqckQR8stzCuWy6oj5Vb7Hfq6t5KKLctuBbUJaTvaJ4IpP0xrqs1qI+V22Zcy+Ct9HQ0G4mv+IPpY+X2F3/cxwK6WJm9ihtjiYWrL/P62DP6mi/zLQn6D/hya6H7GQ3qY8/pNtw9UZ+gjz2nO+gOZ+/SB367WMj7jkDa2DOaNC3uGf0x7n447n+/xLzvBW70+Sdee+w53U73/RrUx57TWlzduAnncCYGyu0V/39SqjrWlqAxDMMwMoqFNxuGYRgZxRyNYRiGkVHM0RiGYRgZxRyNYRiGkVHM0RiGYRgZxRyNYeQAIvIBEVEReWO2bTGMwcYcjWHkBh/DLedyebYNMYzBxhyNYWQZERmHWwH8M3hHIyJFIvI/IrJVRB4RkUdF5MNed6aI/E5EXhSRdSIyLYvmG0ZKzNEYRvZ5P27ByJeBBhF5G25hzjm4tes+i1v3DREZhVs+58OqeiZuscUV2TDaMNJlZLYNMAyDj+HWAQO3ptXHcAuS/lxVO4H9IvJbrz8dt5rz426rEUbglggxjJzFHI1hZBERKcct9niGiCjOcShue4ikSYCtqvqOITLRMAaMdZ0ZRnb5MHC/qs5W1TmqOhO3yGM98CE/VjMFt5AkuEUUK0WkqytNRN6SDcMNI13M0RhGdvkYPVsvv8RthrUHt/rv/+I2yYqqaivOOd0uIn/Brbb8zqEz1zBOHlu92TByFBEZp6rHfPfaH4F3qWri3u6GkfPYGI1h5C6PiMgE3N4jt5iTMfIVa9EYhmEYGcXGaAzDMIyMYo7GMAzDyCjmaAzDMIyMYo7GMAzDyCjmaAzDMIyM8v8DiI7IX7lCBV4AAAAASUVORK5CYII=\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "sns.boxplot(x=\"Age\", y=\"EstimatedSalary\", data=df) #Bad design, Form Age Data brackets and try again"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "18 60\n"
     ]
    },
    {
     "data": {
      "text/plain": [
       "Text(0.5, 1.0, 'Age vs Income Distribution')"
      ]
     },
     "execution_count": 10,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "print(df['Age'].min(), df['Age'].max())\n",
    "bins = [18,25,30,35,40,45,50,55,60,65]\n",
    "labels = ['18-24','25-30', '30-34','35-39','40-44','44-50', '50-54','54-59','60-64' '65+']\n",
    "df['AgeBins'] = pd.cut(df.Age, bins, labels = labels,include_lowest = True)\n",
    "sns.boxplot(x=\"AgeBins\", y=\"EstimatedSalary\", data=df).set_title(\"Age vs Income Distribution\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Data Preperation"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "metadata": {},
   "outputs": [],
   "source": [
    "#Drop UserID AgeBing\n",
    "df.drop(columns = ['User ID', 'AgeBins'], inplace = True)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "metadata": {},
   "outputs": [],
   "source": [
    "# One hot encoding for Male-Female\n",
    "df['Gender'].replace({'Male': 1, 'Female': 0}, inplace = True)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Data Splitting "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "metadata": {},
   "outputs": [],
   "source": [
    "X = df.iloc[:, [0,1,2]].values\n",
    "y = df.iloc[:, 3].values"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.model_selection import train_test_split\n",
    "X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.25, random_state = 0)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Data Modelling"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "[[    1    19 19000]\n",
      " [    1    35 20000]\n",
      " [    0    26 43000]\n",
      " ...\n",
      " [    0    50 20000]\n",
      " [    1    36 33000]\n",
      " [    0    49 36000]] [0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 1 0 0 0 0 0\n",
      " 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0\n",
      " 0 1 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 1 0 0 0 0 0 0 0\n",
      " 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 1 0\n",
      " 0 0 0 0 0 0 0 0 0 0 0 1 1 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0\n",
      " 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 1 0 1 0 1 0 1 1 0 0 0 1 0 0 0 1 0 1\n",
      " 1 1 0 0 1 1 0 1 1 0 1 1 0 1 0 0 0 1 1 0 1 1 0 1 0 1 0 1 0 0 1 1 0 1 0 0 1\n",
      " 1 0 1 1 0 1 1 0 0 1 0 0 1 1 1 1 1 0 1 1 1 1 0 1 1 0 1 0 1 0 1 1 1 1 0 0 0\n",
      " 1 1 0 1 1 1 1 1 0 0 0 1 1 0 0 1 0 1 0 1 1 0 1 0 1 1 0 1 1 0 0 0 1 1 0 1 0\n",
      " 0 1 0 1 0 0 1 1 0 0 1 1 0 1 1 0 0 1 0 1 0 1 1 1 0 1 0 1 1 1 0 1 1 1 1 0 1\n",
      " 1 1 0 1 0 1 0 0 1 1 0 1 1 1 1 1 1 0 1 1 1 1 1 1 0 1 1 1 0 1]\n"
     ]
    }
   ],
   "source": [
    "print(X,y)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.linear_model import LogisticRegression\n",
    "classifier = LogisticRegression(random_state = 0)\n",
    "classifier.fit(X_train, y_train)\n",
    "y_pred = classifier.predict(X_test)\n",
    "from sklearn.metrics import confusion_matrix\n",
    "cm = confusion_matrix(y_test, y_pred)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([[68,  0],\n",
       "       [32,  0]], dtype=int64)"
      ]
     },
     "execution_count": 17,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "cm #This is wrong, our predictor is saying everyone purchased, some problem here."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Adjusting Scaling Issues"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 67,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "[[-0.98019606  0.58164944 -0.88670699]\n",
      " [ 1.02020406 -0.60673761  1.46173768]\n",
      " [-0.98019606 -0.01254409 -0.5677824 ]\n",
      " [-0.98019606 -0.60673761  1.89663484]\n",
      " [-0.98019606  1.37390747 -1.40858358]\n",
      " [-0.98019606  1.47293972  0.99784738]\n",
      " [ 1.02020406  0.08648817 -0.79972756]\n",
      " [ 1.02020406 -0.01254409 -0.24885782]\n",
      " [-0.98019606 -0.21060859 -0.5677824 ]\n",
      " [-0.98019606 -0.21060859 -0.19087153]\n",
      " [-0.98019606 -0.30964085 -1.29261101]\n",
      " [ 1.02020406 -0.30964085 -0.5677824 ]\n",
      " [ 1.02020406  0.38358493  0.09905991]\n",
      " [-0.98019606  0.8787462  -0.59677555]\n",
      " [-0.98019606  2.06713324 -1.17663843]\n",
      " [ 1.02020406  1.07681071 -0.13288524]\n",
      " [-0.98019606  0.68068169  1.78066227]\n",
      " [-0.98019606 -0.70576986  0.56295021]\n",
      " [-0.98019606  0.77971394  0.35999821]\n",
      " [-0.98019606  0.8787462  -0.53878926]]\n"
     ]
    }
   ],
   "source": [
    "#Scale the data\n",
    "\n",
    "X = df.iloc[:, [0,1,2]].values\n",
    "y = df.iloc[:, 3].values\n",
    "\n",
    "from sklearn.model_selection import train_test_split\n",
    "X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.25, random_state = 0)\n",
    "\n",
    "\n",
    "from sklearn.preprocessing import StandardScaler\n",
    "sc = StandardScaler()\n",
    "X_train_scaled = sc.fit_transform(X_train)\n",
    "X_test_scaled = sc.transform(X_test)\n",
    "print(X_train_scaled[:20])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.linear_model import LogisticRegression\n",
    "classifier = LogisticRegression(random_state = 0)\n",
    "classifier.fit(X_train_scaled, y_train)\n",
    "y_pred_lr = classifier.predict(X_test_scaled)\n",
    "from sklearn.metrics import confusion_matrix\n",
    "cm_scaled = confusion_matrix(y_test, y_pred_lr)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 20,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Logistics Regression Results\n",
      "[[65  3]\n",
      " [ 7 25]]\n",
      "****************************************************************************************************\n"
     ]
    }
   ],
   "source": [
    "print('Logistics Regression Results')\n",
    "print(cm_scaled) # looks better\n",
    "print(\"*\"*100)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Some more ML models "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 23,
   "metadata": {},
   "outputs": [],
   "source": [
    "def printConfusionMatrix(model_name, y_true, y_pred):\n",
    "    print(model_name + \" \" + 'results')\n",
    "    cm = confusion_matrix(y_true, y_pred)\n",
    "    print(cm)\n",
    "    print(\"*\"*100)\n",
    "    "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 24,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "KNN results\n",
      "[[64  4]\n",
      " [ 3 29]]\n",
      "****************************************************************************************************\n",
      "Support Vector Machine Results\n",
      "[[64  4]\n",
      " [ 3 29]]\n",
      "****************************************************************************************************\n",
      "Decision Tree Normal results\n",
      "[[63  5]\n",
      " [ 3 29]]\n",
      "****************************************************************************************************\n",
      "Decision Tree Scaled results\n",
      "[[63  5]\n",
      " [ 3 29]]\n",
      "****************************************************************************************************\n",
      "Random Forest Scaled results\n",
      "[[64  4]\n",
      " [ 4 28]]\n",
      "****************************************************************************************************\n"
     ]
    }
   ],
   "source": [
    "from sklearn.neighbors import KNeighborsClassifier\n",
    "classifierknn = KNeighborsClassifier(n_neighbors = 5, metric = 'minkowski')\n",
    "classifierknn.fit(X_train_scaled, y_train)\n",
    "y_pred_knn = classifierknn.predict(X_test_scaled)\n",
    "cm_knn = confusion_matrix(y_test, y_pred_knn)\n",
    "print('KNN results')\n",
    "print(cm_knn)\n",
    "print(\"*\"*100)\n",
    "\n",
    "from sklearn.svm import SVC\n",
    "classifiersvc = SVC(kernel = 'rbf', random_state = 0)\n",
    "classifiersvc.fit(X_train_scaled, y_train)\n",
    "# Predicting the Test set results\n",
    "y_pred_svm = classifiersvc.predict(X_test_scaled)\n",
    "cm_svm = confusion_matrix(y_test, y_pred_svm)\n",
    "print('Support Vector Machine Results')\n",
    "print(cm_svm)\n",
    "print(\"*\"*100)\n",
    "\n",
    "\n",
    "#Decision tree without scaling, lets see wt happens\n",
    "from sklearn.tree import DecisionTreeClassifier\n",
    "classifierdt = DecisionTreeClassifier(criterion = 'entropy', random_state = 0)\n",
    "classifierdt.fit(X_train, y_train)\n",
    "y_pred_dt = classifierdt.predict(X_test)\n",
    "#Lets write a simple function to avoid repetitive print statement\n",
    "printConfusionMatrix(\"Decision Tree Normal\",y_test,y_pred_dt)\n",
    "\n",
    "#Decision tree with scaling, lets see wt happens\n",
    "from sklearn.tree import DecisionTreeClassifier\n",
    "classifierdtsc = DecisionTreeClassifier(criterion = 'entropy', random_state = 0)\n",
    "classifierdtsc.fit(X_train_scaled, y_train)\n",
    "y_pred_dtsc = classifierdtsc.predict(X_test_scaled)\n",
    "printConfusionMatrix(\"Decision Tree Scaled\",y_test,y_pred_dtsc)\n",
    "\n",
    "\n",
    "#Random Forest Modelling\n",
    "from sklearn.ensemble import RandomForestClassifier\n",
    "classifierrf = RandomForestClassifier(n_estimators = 10, criterion = 'entropy', random_state = 0)\n",
    "classifierrf.fit(X_train_scaled, y_train)\n",
    "y_pred_rf = classifierrf.predict(X_test_scaled)\n",
    "printConfusionMatrix(\"Random Forest Scaled\",y_test,y_pred_rf)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Result Interpretation"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 39,
   "metadata": {},
   "outputs": [],
   "source": [
    "result_df = pd.DataFrame()\n",
    "result_df['Actual'] = y_test\n",
    "result_df['LR_predictions'] = y_pred_lr\n",
    "result_df['KNN_preditions'] = y_pred_knn\n",
    "result_df['SVM_predictions'] = y_pred_svm\n",
    "result_df['DT_predictions'] = y_pred_dt\n",
    "result_df['RF_predictions'] = y_pred_rf"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 38,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Actual</th>\n",
       "      <th>LR_predictions</th>\n",
       "      <th>KNN_preditions</th>\n",
       "      <th>SVM_predictions</th>\n",
       "      <th>DT_predictions</th>\n",
       "      <th>RF_predictions</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>...</th>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>95</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>96</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>97</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>98</th>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>99</th>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>100 rows × 6 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "    Actual  LR_predictions  KNN_preditions  SVM_predictions  DT_predictions  \\\n",
       "0        0               0               0                0               0   \n",
       "1        0               0               0                0               0   \n",
       "2        0               0               0                0               0   \n",
       "3        0               0               0                0               0   \n",
       "4        0               0               0                0               0   \n",
       "..     ...             ...             ...              ...             ...   \n",
       "95       1               0               0                0               1   \n",
       "96       0               0               0                0               0   \n",
       "97       1               0               1                1               1   \n",
       "98       1               1               1                1               1   \n",
       "99       1               1               1                1               1   \n",
       "\n",
       "    RF_predictions  \n",
       "0                0  \n",
       "1                0  \n",
       "2                0  \n",
       "3                0  \n",
       "4                0  \n",
       "..             ...  \n",
       "95               0  \n",
       "96               0  \n",
       "97               1  \n",
       "98               1  \n",
       "99               1  \n",
       "\n",
       "[100 rows x 6 columns]"
      ]
     },
     "execution_count": 38,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "result_df"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 53,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.78125"
      ]
     },
     "execution_count": 53,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "\n",
    "\n",
    "accuracy_score(result_df['Actual'], result_df['LR_predictions'])\n",
    "f1_score(result_df['Actual'], result_df['LR_predictions'], average='binary')\n",
    "precision_score(result_df['Actual'], result_df['LR_predictions'], average='binary')\n",
    "recall_score(result_df['Actual'], result_df['LR_predictions'], average='binary')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 64,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "LR_predictions\n",
      "Accuray for LR_predictions  0.9\n",
      "F1 Score for LR_predictions  0.8333333333333334\n",
      "Precision for LR_predictions  0.8928571428571429\n",
      "Recall for LR_predictions  0.78125\n",
      "****************************************************************************************************\n",
      "KNN_preditions\n",
      "Accuray for KNN_preditions  0.93\n",
      "F1 Score for KNN_preditions  0.8923076923076922\n",
      "Precision for KNN_preditions  0.8787878787878788\n",
      "Recall for KNN_preditions  0.90625\n",
      "****************************************************************************************************\n",
      "SVM_predictions\n",
      "Accuray for SVM_predictions  0.93\n",
      "F1 Score for SVM_predictions  0.8923076923076922\n",
      "Precision for SVM_predictions  0.8787878787878788\n",
      "Recall for SVM_predictions  0.90625\n",
      "****************************************************************************************************\n",
      "DT_predictions\n",
      "Accuray for DT_predictions  0.92\n",
      "F1 Score for DT_predictions  0.8787878787878787\n",
      "Precision for DT_predictions  0.8529411764705882\n",
      "Recall for DT_predictions  0.90625\n",
      "****************************************************************************************************\n",
      "RF_predictions\n",
      "Accuray for RF_predictions  0.92\n",
      "F1 Score for RF_predictions  0.875\n",
      "Precision for RF_predictions  0.875\n",
      "Recall for RF_predictions  0.875\n",
      "****************************************************************************************************\n"
     ]
    }
   ],
   "source": [
    "for each in result_df.columns:\n",
    "    if each != 'Actual':\n",
    "        print(each)\n",
    "        print(\"Accuray for \"+ each + \" \",accuracy_score(result_df['Actual'], result_df[each]))\n",
    "        print(\"F1 Score for \"+ each + \" \",f1_score(result_df['Actual'], result_df[each], average='binary'))\n",
    "        print(\"Precision for \"+ each + \" \",precision_score(result_df['Actual'], result_df[each], average='binary'))\n",
    "        print(\"Recall for \"+ each + \" \",recall_score(result_df['Actual'], result_df[each], average='binary'))\n",
    "        print('*'*100)"
   ]
  },
  {
   "cell_type": "raw",
   "metadata": {},
   "source": [
    "# THe confusion matrix is not enough, let try some more evaluation techniques\n",
    "True Positive Rate = True Positives / (True Positives + False Negatives\n",
    "Sensitivity = True Positives / (True Positives + False Negatives)\n",
    "\n",
    "\n",
    "\n",
    "False Positive Rate = False Positives / (False Positives + True Negatives)\n",
    "Specificity = True Negatives / (True Negatives + False Positives)\n",
    "\n",
    "\n",
    "False Positive Rate = 1 - Specificity\n",
    "\n",
    "Positive Predictive Power = True Positives / (True Positives + False Positives)\n",
    "Precision = True Positives / (True Positives + False Positives)\n",
    "\t\n",
    "Recall = True Positives / (True Positives + False Negatives)\n",
    "\n",
    "Recall == Sensitivity"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "https://towardsdatascience.com/roc-curve-explained-using-a-covid-19-hypothetical-example-binary-multi-class-classification-bab188ea869c\n",
    "\n",
    "https://machinelearningmastery.com/roc-curves-and-precision-recall-curves-for-classification-in-python/"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.7.3"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 4
}
