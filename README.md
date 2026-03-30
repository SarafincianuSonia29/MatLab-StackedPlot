# MatLab-StackedPlot
Aplicatie analiza date-stackedplot

## Descriere
Aplicație realizată în MATLAB pentru vizualizarea datelor din fișiere CSV folosind stackedplot.

## Funcționalități
- Încărcare fișiere CSV
- Selectare variabilă X
- Personalizare grafic (titlu, axe, font, culoare)

## Tehnologii
- MATLAB
- App Designer

classdef Stackedplot < matlab.apps.AppBase

    % Properties that correspond to app components
    properties (Access = public)
        UIFigure                matlab.ui.Figure
        Button_Plot             matlab.ui.control.Button
        EditField_Color         matlab.ui.control.EditField
        ColorEditFieldLabel     matlab.ui.control.Label
        EditField_FontSize      matlab.ui.control.EditField
        FontSizeEditFieldLabel  matlab.ui.control.Label
        EditField_YLabel        matlab.ui.control.EditField
        YLabelEditFieldLabel    matlab.ui.control.Label
        EditField_XLabel        matlab.ui.control.EditField
        XLabelEditFieldLabel    matlab.ui.control.Label
        EditField_Title         matlab.ui.control.EditField
        TitleEditFieldLabel     matlab.ui.control.Label
        DropDown_XVar           matlab.ui.control.DropDown
        VariableXDropDownLabel  matlab.ui.control.Label
        Button_LoadTable        matlab.ui.control.Button
    end

    
    properties (Access = private)
        tbl % Variabilă pentru a stoca tabelul încărcat 
    end
    

    % Callbacks that handle component events
    methods (Access = private)

        % Button pushed function: Button_LoadTable
        function Button_LoadTable_Callback(app, event)
            [file, path] = uigetfile('*.csv', 'Selectați un fișier CSV');
          if isequal(file, 0)
        return; % Dacă utilizatorul anulează, nu face nimic
          end
    fullFilePath = fullfile(path, file);
    app.tbl = readtable(fullFilePath); % Încarcă datele în variabila tbl
    
    % Selectează doar coloanele cu tipuri acceptate pentru XVariable
     validColumns = app.tbl.Properties.VariableNames( ...
        varfun(@(x) isnumeric(x) || islogical(x) || isdatetime(x) || isduration(x), app.tbl, 'OutputFormat', 'uniform') ...
    );
    
    % Populează DropDown_XVar cu numele coloanelor tabelului
    app.DropDown_XVar.Items = app.tbl.Properties.VariableNames;
        end

        % Button pushed function: Button_Plot
        function Button_Plot_Callback(app, event)
            if isempty(app.tbl)
        uialert(app.UIFigure, 'Încărcați mai întâi un tabel!', 'Eroare');
        return;
           end

    xvar = app.DropDown_XVar.Value; % Variabila selectată pentru X
    
    % Setează opțiunile grafice (fără YLabel)
    options = {};
    if ~isempty(app.EditField_Title.Value)
        options = [options, 'Title', app.EditField_Title.Value];
    end
    if ~isempty(app.EditField_XLabel.Value)
        options = [options, 'XLabel', app.EditField_XLabel.Value];
    end
    if ~isempty(app.EditField_FontSize.Value)
        options = [options, 'FontSize', str2double(app.EditField_FontSize.Value)];
    end
    if ~isempty(app.EditField_Color.Value)
        options = [options, 'Color', app.EditField_Color.Value];
    end

    % Creează o nouă fereastră pentru stackedplot
    fig = figure('Name', 'Stacked Plot', 'NumberTitle', 'off');
    s = stackedplot(app.tbl, "XVariable", xvar, options{:});

    % Setăm eticheta axei Y separat
     if ~isempty(app.EditField_YLabel.Value)
        ylabel(s.Axes(1), app.EditField_YLabel.Value);
     end
        end
    end

    % Component initialization
    methods (Access = private)

        % Create UIFigure and components
        function createComponents(app)

            % Create UIFigure and hide until all components are created
            app.UIFigure = uifigure('Visible', 'off');
            app.UIFigure.Position = [100 100 640 480];
            app.UIFigure.Name = 'MATLAB App';

            % Create Button_LoadTable
            app.Button_LoadTable = uibutton(app.UIFigure, 'push');
            app.Button_LoadTable.ButtonPushedFcn = createCallbackFcn(app, @Button_LoadTable_Callback, true);
            app.Button_LoadTable.Tag = 'Button_LoadTable';
            app.Button_LoadTable.Position = [438 431 142 22];
            app.Button_LoadTable.Text = 'Incarca tabel';

            % Create VariableXDropDownLabel
            app.VariableXDropDownLabel = uilabel(app.UIFigure);
            app.VariableXDropDownLabel.HorizontalAlignment = 'right';
            app.VariableXDropDownLabel.Position = [29 431 59 22];
            app.VariableXDropDownLabel.Text = 'Variable X';

            % Create DropDown_XVar
            app.DropDown_XVar = uidropdown(app.UIFigure);
            app.DropDown_XVar.Tag = 'DropDown_XVar';
            app.DropDown_XVar.Position = [103 431 100 22];

            % Create TitleEditFieldLabel
            app.TitleEditFieldLabel = uilabel(app.UIFigure);
            app.TitleEditFieldLabel.HorizontalAlignment = 'right';
            app.TitleEditFieldLabel.Position = [230 431 27 22];
            app.TitleEditFieldLabel.Text = 'Title';

            % Create EditField_Title
            app.EditField_Title = uieditfield(app.UIFigure, 'text');
            app.EditField_Title.Tag = 'EditField_Title';
            app.EditField_Title.Position = [272 427 100 26];

            % Create XLabelEditFieldLabel
            app.XLabelEditFieldLabel = uilabel(app.UIFigure);
            app.XLabelEditFieldLabel.HorizontalAlignment = 'right';
            app.XLabelEditFieldLabel.Position = [41 378 42 22];
            app.XLabelEditFieldLabel.Text = 'XLabel';

            % Create EditField_XLabel
            app.EditField_XLabel = uieditfield(app.UIFigure, 'text');
            app.EditField_XLabel.Tag = 'EditField_XLabel';
            app.EditField_XLabel.Position = [99 378 100 22];

            % Create YLabelEditFieldLabel
            app.YLabelEditFieldLabel = uilabel(app.UIFigure);
            app.YLabelEditFieldLabel.HorizontalAlignment = 'right';
            app.YLabelEditFieldLabel.Position = [41 336 42 22];
            app.YLabelEditFieldLabel.Text = 'YLabel';

            % Create EditField_YLabel
            app.EditField_YLabel = uieditfield(app.UIFigure, 'text');
            app.EditField_YLabel.Tag = 'EditField_YLabel';
            app.EditField_YLabel.Position = [99 336 100 22];

            % Create FontSizeEditFieldLabel
            app.FontSizeEditFieldLabel = uilabel(app.UIFigure);
            app.FontSizeEditFieldLabel.HorizontalAlignment = 'right';
            app.FontSizeEditFieldLabel.Position = [41 302 52 22];
            app.FontSizeEditFieldLabel.Text = 'FontSize';

            % Create EditField_FontSize
            app.EditField_FontSize = uieditfield(app.UIFigure, 'text');
            app.EditField_FontSize.Tag = 'EditField_FontSize';
            app.EditField_FontSize.Position = [99 302 100 22];

            % Create ColorEditFieldLabel
            app.ColorEditFieldLabel = uilabel(app.UIFigure);
            app.ColorEditFieldLabel.HorizontalAlignment = 'right';
            app.ColorEditFieldLabel.Position = [41 263 34 22];
            app.ColorEditFieldLabel.Text = 'Color';

            % Create EditField_Color
            app.EditField_Color = uieditfield(app.UIFigure, 'text');
            app.EditField_Color.Tag = 'EditField_Color';
            app.EditField_Color.Position = [99 263 100 22];

            % Create Button_Plot
            app.Button_Plot = uibutton(app.UIFigure, 'push');
            app.Button_Plot.ButtonPushedFcn = createCallbackFcn(app, @Button_Plot_Callback, true);
            app.Button_Plot.Tag = 'Button_Plot';
            app.Button_Plot.Position = [438 378 142 22];
            app.Button_Plot.Text = 'Generare Grafic';

            % Show the figure after all components are created
            app.UIFigure.Visible = 'on';
        end
    end

    % App creation and deletion
    methods (Access = public)

        % Construct app
        function app = Stackedplot

            % Create UIFigure and components
            createComponents(app)

            % Register the app with App Designer
            registerApp(app, app.UIFigure)

            if nargout == 0
                clear app
            end
        end

        % Code that executes before app deletion
        function delete(app)

            % Delete UIFigure when app is deleted
            delete(app.UIFigure)
        end
    end
end
