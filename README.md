
        public object GetValue(object obj, string propertyName, bool throwErrors)
        {
            string[] parts = propertyName.Split('.');
            Type enityType = obj.GetType();
            PropertyInfo pinfo = enityType.GetProperty(parts[0]);

            if (pinfo == null && throwErrors)
            {
                throw new Exception(string.Format(ErrorStrings.ERR_PROPERTY_IS_MISSING, enityType.Name, propertyName));
            }

            if (pinfo == null)
            {
                return null;
            }

            if (parts.Length == 1)
            {
                return pinfo.GetValue(obj, null);
            }

            object pval = pinfo.GetValue(obj, null) ?? throw new Exception(string.Format(ErrorStrings.ERR_PPROPERTY_ISNULL, enityType.Name, pinfo.Name));

            return GetValue((dynamic)pval, string.Join(".", parts.Skip(1)), throwErrors);
        }

        public object GetValue(IDictionary<string, object> obj, string propertyName, bool throwErrors)
        {
            string[] parts = propertyName.Split('.');

            bool isOk = obj.TryGetValue(propertyName, out object propValue);

            if (!isOk && throwErrors)
            {
                throw new Exception(string.Format(ErrorStrings.ERR_PROPERTY_IS_MISSING, obj.GetType().Name, propertyName));
            }

            if (!isOk)
            {
                return null;
            }

            if (parts.Length == 1)
            {
                return propValue;
            }

            if (propValue == null)
            {
                throw new Exception(string.Format(ErrorStrings.ERR_PPROPERTY_ISNULL, obj.GetType().Name, propertyName));
            }

            return GetValue((dynamic)propValue, string.Join(".", parts.Skip(1)), throwErrors);
        }
